# Optional

到目前为止，著名的NullPointerException是导致Java应用程序失败的最常见原因。过去，为了解决空指针异常，Google公司著名的Guava项目引入了Optional类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。如今，受到Google Guava的启发，Optional类已经成为Java 8类库的一部分。


**Optional**：按照字面英文解释为“可选的” 意思，但此处的语义是**指某个值可能有也可能没有（null）**。

Optional 被定义为一个简单的容器，其值可能是null或者不是null。在Java 8之前一般某个函数应该返回非空对象但是偶尔却可能返回了null，而在Java 8 以后，不推荐你返回null而是返回Optional。
