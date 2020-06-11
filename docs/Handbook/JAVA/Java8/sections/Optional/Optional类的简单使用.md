### 1.2 Optional的简单使用

> Optional类的包名：java.util.Optional<T>

为了防止抛出java.lang.NullPointerException 异常，我们经常会写出这样的代码：

```
Student student = person.find("Lucy"); 

	 if (student != null) { 

     student.doSomething(); 

 	 } 
```

使用Optional的代码写法：

 ```
Student student = person.find("Lucy"); 
  if (student.isPresent()) { 
  student.get().doSomething(); 
  } 
 ```

> 说明：如果isPresent()返回false，说明这是个空对象；否则，我们就可以把其中的内容取出来做相应的操作了。
> 单从代码量上来说，Optional的代码量并未减少，甚至比原来的代码还多。但好在，你绝对不会忘记判空，因为这里我们得到的不是Student类的对象，而是Optional。