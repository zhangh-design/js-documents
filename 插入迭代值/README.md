## 插入迭代值

当使用迭代值（也就是在不同的位置进行增加或减少的值）的时候，尽可能的合并语句。

请看一下代码：

```
var name = values[i];
i++;
```
前面这两句语句各只有一个目的：第一个从values数组中获取值，然后存储在`name`中：第二个给变量`i`增加1。这两句可以通过迭代值插入第一个语句组合成一个语句，如下所示：


```
var name = values[i++];
```
这一个语句可以完成和前面两个语句一样的事情。因为自增操作符是后缀操作符，`i`的值只有在语句其它部分结束之后才会增加。一旦出现类似情况，都要尝试将迭代值插入到最后使用它的语句中去。

