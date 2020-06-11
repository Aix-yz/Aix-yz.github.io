### 1.3 Optional类的主要方法

**1. of(T value)**

为非Null的值创建一个Optional。

of()方法通过工厂方法创建Optional类。需要注意，创建对象时传入的参数不能为null。如果传入参数为null，则抛出NullPointerException 。

```
//调用工厂方法创建Optional实例
		Optional<String> myValue = Optional.of("OptionalTest");
		//传入参数为null，抛出NullPointerException.
		Optional<String> someNull = Optional.of(null);
```

**2. ofNullable(T value)**

为指定的值创建一个Optional，如果指定的值为Null，则返回一个空的Optional。与of的唯一区别是可以接受参数为null的情况。

```
		//下面创建了一个不包含任何值的Optional实例
		//eg:值为'null'
		Optional empty = Optional.ofNullable(null);
```

**3. isPresent()**

如果值存在则返回true，否则返回false

```
		//isPresent()方法用来检查Optional实例中是否包含值
		if (myValue.isPresent()) {
		//在Optional实例内调用get()返回已存在的值
		System.out.println(myValue.get());//输出OptionalTest }
```

**4. get()**

如果Optional有值则将其返回，否则抛出异常 NoSuchElementException。

在上面的示例中，get方法用来得到Optional实例中的值。下面是一个抛出NoSuchElementException的示例：

```
//执行下面的代码会输出：No value present 
		try {
		  //在空的Optional实例上调用get()，抛出NoSuchElementException
		  System.out.println(empty.get());
		} catch (NoSuchElementException ex) {
		  System.out.println(ex.getMessage());
		}
```

**5. ifPresent(Consumer <? super T> consumer)**

如果Optional实例有值则为其调用consumer，否则不做处理。

要理解ifPresent()方法，首先需要了解Consumer类。简单地说，Consumer类包含一个抽象方法。该抽象方法对传入的值进行处理，但没有返回值。

Java8支持不用接口直接通过lambda表达式传入参数。

如果Optional实例有值，调用ifPresent()可以接受接口段或lambda表达式。

```
//ifPresent()方法接受lambda表达式作为参数。
		//lambda表达式对Optional的值调用consumer进行处理。
		myValue.ifPresent((value) -> {
		  System.out.println("The length of the value is: " + value.length());
		});
```

**6. orElse(T other)**

如果有值则返回，否则返回orElse方法传入的参数，即参数为默认返回值。

```
//如果值不为null，orElse方法返回Optional实例的值。
		//如果为null，返回传入的消息。
		//输出：There is no value present!
		System.out.println(empty.orElse("There is no value present!"));
		//输出：OptionalTest
		System.out.println(myValue.orElse("There is some value!"));
```

**7. orElseGet(Supplier<? extends T> other)**

与orElse方法类似，区别在于得到的默认值。orElse方法将传入的参数字符串作为默认值，orElseGet方法可以接受Supplier接口的实现用来生成默认值。

```
//orElseGet可以接受一个lambda表达式生成默认值。
		//输出：Default Value
		System.out.println(empty.orElseGet(() -> "Default Value"));
		//输出：OptionalTest
		System.out.println(myValue.orElseGet(() -> "Default Value"));
```

**8. orElseThrow(Supplier<? extends X> exceptionSupplier)**

如果有值则将其返回，否则抛出supplier接口创建的异常。

在orElseGet()方法中，我们传入一个Supplier接口。然而，在orElseThrow中我们可以传入一个lambda表达式或方法，如果值不存在就抛出异常。

```
try {
		  //orElseThrow与orElse方法类似。与返回默认值不同，
		  //orElseThrow会抛出lambda表达式或方法生成的异常 
		 
		  empty.orElseThrow(ValueAbsentException::new);
		} catch (Throwable ex) {
		  //输出: No value present in the Optional instance
		  System.out.println(ex.getMessage());
		}
```

```
class ValueAbsentException extends Throwable {
 
  public ValueAbsentException() {
    super();
  }
 
  public ValueAbsentException(String msg) {
    super(msg);
  }
 
  @Override
  public String getMessage() {
    return "No value present in the Optional instance";
  }
}
```

**9. map(Function<? super T,? extends U> mapper)**

如果有值，则对其执行调用mapping函数得到返回值。如果返回值不为null，则创建包含mapping返回值的Optional作为map方法返回值，否则返回空Optional。

map方法用来对Optional实例的值执行一系列操作。通过一组实现了Function接口的lambda表达式传入操作。

```
		//map方法执行传入的lambda表达式参数对Optional实例的值进行修改。
		//为Lambda表达式的返回值创建新的Optional实例作为map方法的返回值。
		Optional<String> upperName = myValue.map((value) -> value.toUpperCase());
		System.out.println(upperName.orElse("No value found"));
```

**10. flatMap (Function<? super T,Optional \<U> > mapper)**

如果有值，为其执行mapping函数返回Optional类型返回值，否则返回空Optional。flatMap与map（Funtion）方法类似，区别在于flatMap中的mapper返回值必须是Optional。调用结束时，flatMap不会对结果用Optional封装。

flatMap方法与map方法类似，区别在于mapping函数的返回值不同。map方法的mapping函数返回值可以是任何类型T，而flatMap方法的mapping函数必须是Optional。

下面参照map函数，使用flatMap重写的示例

```
		//map方法中的lambda表达式返回值可以是任意类型，在map函数返回之前会包装为Optional。 
		//但flatMap方法中的lambda表达式返回值必须是Optionl实例。 
		upperName = myValue.flatMap((value) -> Optional.of(value.toUpperCase()));
		System.out.println(upperName.orElse("No value found"));
```

**11. filter (Predicate<? super T> predicate)**

如果有值并且满足断言条件返回包含该值的Optional，否则返回空Optional。

filter个方法通过传入限定条件对Optional实例的值进行过滤。对于filter函数我们可以传入实现了Predicate接口的lambda表达式。

```
	//filter方法检查给定的Option值是否满足某些条件。
		//如果满足则返回同一个Option实例，否则返回空Optional。
		Optional<String> longName = myValue.filter((value) -> value.length() > 6);
		System.out.println(longName.orElse("The name is less than 6 characters"));//输出OptionalTest
		 
		//另一个例子是Optional值不满足filter指定的条件。
		Optional<String> anotherName = Optional.of("Test");
		Optional<String> shortName = anotherName.filter((value) -> value.length() > 6);
		//输出：name长度不足6字符
		System.out.println(shortName.orElse("The name is less than 6 characters"));
```

**12. empty()**

返回一个空Optional实例

```
Optional.<String>empty(); // 返回一个空Optional<String>
```

**Optional有这么多方法，那Optional的初衷是什么？而且Optional也是一个对象，所以它本身也有可能是null，这可如何是好。所以，个人认为，Optional比较适用的地方是作为返回值，这样可以给使用者一个善意的提醒。**