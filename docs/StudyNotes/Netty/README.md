# Netty

## 1 概念及体系结构

### 1.1 网络编程

早期JavaAPI只支持本地系统套接字库提供的所谓阻塞函数，即阻塞I/O

### 1.2 Netty的核心组件

#### 1.2.1 Channel

- Java的一个基本构造，代表一个实体（硬件设备、文件、网络套接字）的开放连接

#### 1.2.2 回调

- 当一个回调被触发时，相关的事件可以被一个 interface-ChannelHandler 的实现处理，例如channelActive会在连接时候触发

#### 1.2.3 Future

- 可以看作是一个异步操作结果的占位符，提供了另一种在操作完成时通知应用程序的方式。它将在未来的某个时刻完成，并提供对其结果的访问。
- ChannelFuture提供了几种额外的方法，这些方法使得我们能够注册一个或者多个ChannelFutureListener实例。监听器的回调方法operationComplete()，将会在对应的操作完成时被调用 。由ChannelFutureListener提供的通知机制消除了手动检查对应的操作是否完成的必要。
- 每个 Netty 的出站 I/O 操作都将返回一个 ChannelFuture；其不会阻塞，基于此，Netty完全是异步和事件驱动的。

#### 1.2.4 事件和ChannelHander

- 基于事件

  - 入站数据或者相关的状态更改而触发的事件
    - 连接被激活或连接失活
    - 数据读取
    - 用户事件
    - 错误事件
  - 出站事件是未来将会触发的某个动作的操作结果
    - 打开或者关闭到远程节点的连接
    - 将数据写到或者冲刷到套接字

- 触发动作

  - 记录日志
  - 数据转换
  - 流控制
  - 应用程序逻辑

  每个事件都可以被分发给 ChannelHandler 类中的某个用户实现的方法。

#### 1.2.5 EventLoop

Netty 通过触发事件将 Selector 从应用程序中抽象出来，消除了所有本来将需要手动编写的派发代码。在内部，将会为每个 Channel 分配一个 EventLoop，用以处理所有事件。

- 注册感兴趣的事件
- 将事件派发给 ChannelHandler
- 安排进一步的动作

EventLoop 本身只由一个线程驱动，其处理了一个 Channel 的所有 I/O 事件，并且在该EventLoop 的整个生命周期内都不会改变。

### 1.3 简单的服务器和客户端

#### 1.3.1 服务器

##### 1.3.1.1 服务器功能

所有的 Netty 服务器都需要以下两部分：

- 至少一个 ChannelHandler—该组件实现了服务器对从客户端接收的数据的处理，即它的业务逻辑
- 引导—这是配置服务器的启动代码。至少，它会将服务器绑定到它要监听连接请求的端口上

##### 1.3.1.2 引导服务器

引导服务器包括

- 绑定到服务器将在其上监听并接受传入连接请求的端口
- 配置 Channel，以将有关的入站消息通知给 EchoServerHandler 实例

EchoServer类

```
public class EchoServer{

	private static port;
	public EchoServer(int port){
		this.port = port;
	}
	//设置端口，如果端口不正确便抛出错误
	public static void main(String[] args){
		if (args.length != 1){
			System.err.pritln(" Usage "+EchoServer.class.getSimpleName() + " <port>");
		}
		int port = Integer.parseInt(args[0]);
		//调用start()方法
		new EchoServer(port).start();
	}

	public void start() throws Exception {
		final EchoServerHandler serverHandler = new EchoServerHandler();
		//创建EventLoopGroup
		EventLoopGroup group = new NioEventLoopGroup();
		try {
			//创建ServerBootstrap
			ServerBootstrap b = new ServerBootstrap();
			b.group(group)
				//指定所使用的NIO传输Channel
				.channel(NioServerSocketChannel.class) 
				//使用指定的端口设置套接字地址
				.localAddress(new InetSocketAddress(port)) 
				//添加一个EchoServerHandler到Chanel的ChannelPipline
				.childHandler(new ChannelInitializer<SocketChannel>(){
				@Override
				public void initChannel(SocketChannel ch)
					throws Exception {
						//EchoServerHandler被标注为@Shareable，所以我们可以总是使用同样的实例
						ch.pipeline().addLast(serverHandler);
					}
				});
			//异步的绑定服务器，调用sync()方法阻塞等待直到绑定完成
			ChannelFuture f = b.bind().sync();
			//获取Channel的CloseFuture，并且阻塞当前现成直到它完成
			f.channel().closeFuture().sync();
			} finally {
				//关闭EventLoopGroup，释放所有资源
				group.shutdownGracefully().sync();
			}
	}
}
```

当一个新的连接被接受时，一个新的子 Channel 将会被创建

ChannelInitializer 将会把一个你的EchoServerHandler 的实例添加到该 Channel 的 ChannelPipeline 中。

#### 1.3.2 客户端

##### 1.3.2.1 客户端功能

1. 连接到服务器
2. 发送一个或多个消息
3. 对于每个消息，等待并接收从服务器发回的相同消息
4. 关闭连接

客户端涉及的两个主要代码部分也是业务逻辑和引导

##### 1.3.2.2 引导客户端

```
public class EchoClient {
	private final String host;
	private final int port;
	public EchoClient(String host, int port) {
		this.host = host;
		this.port = port;
	}
	public void start() throws Exception {
		EventLoopGroup group = new NioEventLoopGroup();
		try {
			//创建Bootstrap
			Bootstrap b = new Bootstrap();
			b.group(group)
				.channel(NioSocketChannel.class)
				.remoteAddress(new InetSocketAddress(host, port))
				.handler(new ChannelInitializer<SocketChannel>() {
				@Override
				public void initChannel(SocketChannel ch)throws Exception {
					ch.pipeline().addLast(
					new EchoClientHandler());
					}
				});
			ChannelFuture f = b.connect().sync();
			f.channel().closeFuture().sync();
		} finally {
			group.shutdownGracefully().sync();
		} 
	}
	public static void main(String[] args) throws Exception {
		if (args.length != 2) {
			System.err.println(
				"Usage: " + EchoClient.class.getSimpleName() + " <host> <port>");
			return;
		}
		String host = args[0];
		int port = Integer.parseInt(args[1]);
		new EchoClient(host, port).start();
	} 
}
```

