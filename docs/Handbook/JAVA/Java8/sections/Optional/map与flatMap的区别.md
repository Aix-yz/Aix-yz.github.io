### 1.4 map 与 flatMap 的区别

map(mapper) 与 flatMap(mapper) 功能上基本是一样的，只是最后的返回值不一样。map(mapper)方法中的mapper可以返回任意类型，但是flatMap(mapper)方法中的mapper只能返回Optional类型。

如果mapper返回结果result的不是null，那么map就会返回一个Optional,但是 flatMap不会对result进行任何包装。

```
Optional<String> o;
o.map((value)->Optional.of(value)) //返回的类型是Optional<Optional<String>>
o.flatMap((value)->Optional.of(value)) //返回的类型是Optional<String>
```
