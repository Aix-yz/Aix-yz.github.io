### 1.5 Optional应用示例

```
import java.util.NoSuchElementException;
import java.util.Optional;
 
public class OptionalTest {
 
	public static void main(String[] args) {
		 // 创建Optional
        String mayBeNull = null;
        Optional<String> opt1 = Optional.of(" Hello! ");
        Optional<String> opt2 = Optional.ofNullable(mayBeNull);
        Optional<String> opt3 = Optional.empty();
        opt1.ifPresent(System.out::println); // " Hello! "
        opt2.ifPresent(System.out::println);
        opt3.ifPresent(System.out::println);
 
        //方法测试示例
        ofTest();
        ofNullableTest();
        isPresentTest();
        ifPresentTest();
        orElseTest();
        orElseGetTest();
        mapTest();
        flatMapTest();
        filterTest();
 
	}
	
	/**
     * of后面接给optional设置的值 但是不能为空 如果为空会报空指针异常
     */
    public static void ofTest() {
        Optional<String> optional = Optional.of("123");
        System.out.println(optional.get());
        try {
            optional = Optional.of(null);
            System.out.println("null值--"+optional.get());  //get方法是获取optional的值 类型取决于声明的时候
        } catch (NullPointerException e) {
            System.out.println("空指针异常");
        }
    }
 
    /**
     * ofNullable 和of类似 但是ofNullable可以设置null值  如果是Null值得话取值会报NoSuchElementException 异常
     */
    public static void ofNullableTest() {
        Optional<String> optional = Optional.ofNullable("123");
        System.out.println(optional.get());
        try {
            optional = Optional.ofNullable(null);
            System.out.println("null值---"+optional.get());
        } catch (NoSuchElementException e) {
            System.out.println("NoSuchElementException 异常");
        }
    }
 
    /**
     * ifPresent用来判断optional中有没有值存在 如果有则为真
     */
    public static void isPresentTest() {
        Optional<String> optional = Optional.ofNullable(null);
        if (optional.isPresent()) {
            System.out.println(optional.get());
        } else {
            System.out.println("值为空");
        }
    }
 
    /**
     * ifPresent和isPresent类似 只不过它支持λ表达式
     */
    public static void ifPresentTest() {
        Optional<String> optional = Optional.ofNullable("123");
        optional.ifPresent(var -> {
            System.out.println(var);
        });
    }
 
    /**
     * orElse方法，如果值为空的话会用参数中的值去替换 即设置默认值
     */
    public static void orElseTest() {
        Optional<String> optional = Optional.ofNullable("123");
        System.out.println(optional.orElse("有没有"));
        optional = Optional.ofNullable(null);
        System.out.println(optional.orElse("有没有000"));
    }
 
    /**
     * orElseGet方法 和orElse类似 不过此方法接受Supplier接口的实现用来生成默认值
     */
    public static void orElseGetTest() {
        Optional<String> optional = Optional.ofNullable("123");
        System.out.println(optional.orElseGet(() -> "123456"));
        optional = Optional.ofNullable(null);
        System.out.println(optional.orElseGet(() -> "1234567"));
    }
 
    /**
     * map方法  如果有值则会对值进行mapping中的处理 处理结果存在则创建并返回Optional类型的结果 否则返回空
     */
    public static void mapTest() {
        Optional<String> optional = Optional.ofNullable("abc");
        System.out.println(optional.map(var -> var.toUpperCase()).get());
    }
 
    /**
     * flatMap和map类似 只不过mapping中必须返回Option类型的数据
     */
    public static void flatMapTest() {
        Optional<String> optional = Optional.ofNullable("abc");
        System.out.println(optional.flatMap(var -> Optional.of(var.toUpperCase())).get());
    }
 
    /**
     * filter对optional进行过滤,mapping中为过滤的条件  如果不满足条件 返回一个为空的Optional
     */
    public static void filterTest() {
        try {
            Optional<String> optional = Optional.ofNullable("一二三四五六七八");
            System.out.println(optional.filter(var -> var.length() > 6).get());
            System.out.println(optional.filter(var -> var.length() < 6).get());
        } catch (NoSuchElementException e) {
            System.out.println("optional的值为空");
        }
    }
 
}
```

