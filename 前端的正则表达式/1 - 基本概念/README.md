## 基本概念

![image](http://m.qpic.cn/psc?/V12UXEll2JjLTU/S1G4*2hi*D5aPIJug2nMa46eV9hwJnuM2RkChpZxfxD5zrxMjfoedgz*K1lmXmyCvgMDokThwOeB.vpQALxy8imIZAsN2VrlmYHLjegxrUw!/b&bo=kAE.AQAAAAARB54!&rf=viewer_4&t=5)


> #### 创建正则表达式的方法

在 JavaScript 中可以通过两种方式去构造正则表达式。
1. 包含在两个正斜杠中。
2. 使用 RegExp 构造函数。

```
const regex = /dog/;
```

```
const regex = new RegExp('dog');
```

创建对象之后，你可以在对象上调用 test 方法，方法得到字符串后，如果模式匹配，则返回 true：

```
regex.test('dog'); // true

regex.test('hot-dog'); // true
```


> #### 简单模式

这是正则表达式最简单的类型。能够直接在字符串中找到匹配的类型。/dog/ 仅在字符以相同顺序一起出现时才会匹配。

```
/dog/.test('hot-dog'); // true

/dog/.test('do games'); // false
```


> #### 特殊字符

除了寻找某个字符串的简单出现，还可以做更多的事情。一种方法是使用特殊字符。它们不会被解释为被搜索字符串的直接内容，但能够以通用的方式对其进行描述。

##### 1. 任何字符

它由一个点  .  表示。用来匹配除了换行符以外的任何单个字符。

```
const regex = /.og/;

regex.test('fog'); // true
regex.test('dog'); //true
```
通配符是特殊字符之一。如果想要匹配的是一个点 . 字符该怎么办？


##### 2. 反斜杠
反斜杠 / 用于将特殊字符的含义切换为普通字符。所以是可以在文本中搜索点 . 字符的，并且这个点不会被解释为特殊字符。

```
const regex1 = /dog./;
regex1.test('dog.'); // true
regex1.test('dog1'); // true

const regex2 = /dog\./;
regex1.test('dog.'); // true
regex1.test('dog1'); // false
```

##### 3. 字符集
用方括号 [ ] 表示。这个模式用来匹配一个字符，该字符可能是括号中的任何字符。

```
/[dfl]og/.test('dog'); // true
/[dfl]og/.test('fog'); // true
/[dfl]og/.test('log'); // true
```

需要注意的是字符集内的特殊字符（如点 .）不再特殊，因此在这里不再需要反斜杠。我们甚至可以进一步定义一些字符：


```
/[A-z]/.test('a'); // true
/[A-z]/.test('Z'); // true
```

请注意，在字母范围方面，大写字母优先。这意味着 /[a-Z]/ 会引发错误。

```
const pattern = /[a-Z]/;
```

你可以通过添加 ^ 符号轻松获得否定字符集。它会匹配方括号中未包含的所有内容。

```
/[^df]og/.test('dog'); // false
/[^df]og/.test('fog'); // false
/[^df]og/.test('log'); // true
```
一个重要的注意事项：[A-z] 范围实际上将匹配多个字母。正如你在 ASCII 表上看到的那样，[A-z] 也将与符号[、 \、 ]、 ^、 _ 和 ` 相匹配，所以请谨慎使用 [A-Za-z]，而是使用下面介绍的标志来忽略大小写。


##### 4. 多次重复

一个非常有用的功能是匹配某个表达式出现的确切次数。你可以用花括号 { } 来实现。让我们创建一个函数，该函数将检查字符串是否为有效的电话号码。以下面的格式为例：

```
function isPhoneNumber(number){
    return /\+[0-9]{2} [0-9]{3} [0-9]{3} [0-9]{3}/.test(number);
}

isPhoneNumber('+12 123 123 123'); // true
isPhoneNumber('123212'); // false
```
请注意，我们在此处进行了一些自定义：
1. {x} 完全匹配 x 次出现
2. {x,} 至少匹配 x 次
3. {x,y} 至少匹配 x 次且不超过 y 次


##### 4. 零个或多个重复
带有星号 * 的表达式可以匹配 0 次或更多次。它实际上等效于 {0，}

这样我们可以轻松构造一个可以匹配任意数量字符的模式：/.*/

```
var ab = 'hello world'
if(/.*/.test(ab)){
	console.info('匹配');
}
```


> #### 标志

你可以在正则表达式中再添加一个模式。标志是一种影响搜索的修饰符。如果用斜杠定义正则表达式的话，就在斜杠后添加它们。如果用 RegExp 构造函数，则将它们作为第二个参数。最重要的标志是：

##### 1. i：忽略大小写 
使用这个标志，搜索时不区分大小写：

```
/dog/i.test('dOg'); // true
new RegExp('dog', 'i').test('DoG'); // true
```

##### 2. g：全局匹配
多亏了这个标志，所有匹配项都能够被找到。如果没有它，将会在找到第一个匹配项后停止。


##### 3. String.prototype.replace

你很快就能掌握它，因为你可能已经知道函数 replace。它会返回一个新字符串，如果字符串的内容能够与模式匹配，则会替换其内容。你可以使用字符串或正则表达式之类的模式。但棘手的是如果你用字符串去执行这个操作，则无法替换所有出现的模式，只能替换一个模式。如果使用前面提到的标志，就可以轻松地处理：

```
const lorem = 'lorem_ipsum_dolor_sit_amet';

lorem.replace('_', ' '); // 'lorem ipsum_dolor_sit_amet'

lorem.replace(/_/g, ' '); // 'lorem ipsum dolor sit amet'
```

```
var pp = '["child-a", ["child-c",[["child-d"]]],"child-b"]'
var pp1 = (pp.replace(/(\[|\]|"|'|\s+)/g, "")).split(",");
console.info(pp1); // ["child-a", "child-c", "child-d", "child-b"]
```
在 `2 - 写出更优雅、更精确的正则表达式` 中，我们将会讨论更多的标志。

> #### 总结

有了所有这些信息，你就可以开始编写自己的正则表达式并将其使用。我强烈建议你使用这个很棒的工具【https://regex101.com/】， 它会为你提供帮助。在本文的后续部分中，当正则表达式可以发挥更大作用时，我们将会学习更多高级概念，包括更深入地研究 JavaScript 提供的 RegExp 对象。到那时，试着练习已经掌握的知识，你将会真正领略到正则表达式的强大。下次见！
