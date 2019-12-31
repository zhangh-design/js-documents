## 写出更优雅、更精确的正则表达式

今天，我们回到`JavaScript`中的正则表达式。如果你还是新手，请查看上一篇文章。这次，我们将学习如何编写更优雅的模式并定义搜索字符串的位置。

> #### 定义重复的较短方法

我们知道星号 * 可以使表达式匹配 0 次或多次。这相当于{0，}。实际上还有其他更短的形式，使用它们可以使样式更优雅，更短。


##### 1. 一次或多个重复

使用加号 + ，我们可以表示该表达式可能匹配一次或多次。这类似于星号，但在这里必须至少匹配一次。等效于{1,}。


```
/1+23/.test('123'); // true
/1+23/.test('111123'); // true
/1+23/.test('23'); // false
```

这意味着 /.+/ 匹配至少出现一次的任何字符。


```
/.+/.test(''); // false
/.*/.test(''); // true
```

例如检查一个字符串是否包含另一个子字符串，但是不以它结尾：

```
function hasQuestionMarkBeforeEnd(string) {
  	return /\?.+/.test(string);
}

hasQuestionMarkBeforeEnd('Do you know regex yet?'); // false
hasQuestionMarkBeforeEnd('Do you know regex yet? Yes, I do!'); // true
```
请注意，问号是一个特殊字符，因此我们需要在其前面加一个反斜杠。


可以更进一步，写一个更加通用的函数：


```
function containsPatternBeforeEnd(string, pattern) {
  return RegExp(`${pattern}.+`).test(string);
}

containsPatternBeforeEnd('cat, dog', 'cat'); // true
containsPatternBeforeEnd('cat, dog', 'dog'); // false
```


##### 2. 可选字符
如上所述，问号是一个特殊字符。使用它可以创建带有可选字符的模式。它相当于 {0,1}。

验证正整数（大于0的整数）
```
function wereFilesFound(string) {
  	return /[1-9][0-9]* files? found/.test(string);
}

wereFilesFound('0 files found');  // false
wereFilesFound('No files found'); // false
wereFilesFound('1 file found');   // true
wereFilesFound('2 files found');  // true
wereFilesFound('10 files found'); // true

```


```
function wereFilesFound(string) {
  	return /[1-9][0-9]{0,} files{2} found/.test(string);
}

wereFilesFound('10 filess found') // true
```

> #### 用较短的方法定义一组可能出现的字符

以前我们使用方括号 [] 来定义一组可能出现的字符。在正则表达式中，你可以参考一些实现的集合。

##### 1. 字母数字字符

如果你想匹配所有字母和数字字符，则需要这样的模式：/[A-Za-z0-9_]/。相当复杂不是吗？不过，有一种更短的方法：\w。请当心：它们都不能匹配任何特定于语言的字符！

代码 | 功能
---|---
. | 匹配任意1个字符（除了 \n 换行符）
[ ] | 匹配[ ]中列举的字符
\d | 匹配数字，即0-9
\D | 匹配非数字，即不是数字
\s | 匹配空白，即 空格，tab键
\S | 匹配非空白
\w | 匹配非特殊字符，即a-z、A-Z、0-9、_、汉字（汉字要视操作系统）
\W | 匹配特殊字符，即非字母、非数字、非汉字、非_

##### 2. 非字母数字字符

与上述模式相反： /\^[A-Za-z0-9_]/ 。等价于 \W。它有相同的缺陷，不能处理特定于语言的字符：


```
function isAlphanumeric(string) {
  return /\w/.test(string);
}
 
function isNotAlphanumeric(string) {
  return /\W/.test(string);
}

isAlphanumeric('Ó'); // false
isNotAlphanumeric('Ó'); // true
```

##### 3. 处理数字
之前我们了解到要匹配任何数字，我们可以使用类似 [0-9] 的模式。还可以用 \d。它能够匹配任何数字：

```
function isItADigit(string) {
  return /\d/.test(string);
}

isItADigit('5'); // true
isItADigit('a'); // false
isItADigit('1a');// true
```

```
function isItADigit(string) {
  	return /\d{2}\w*/.test(string);
}
isItADigit('12ab&'); // true
```


##### 4. 处理空格
在字符串中，有几种类型的空格字符：
- 空格 ” ”
- tab “/t”
- 新行 “\n”
- 回车符 “\r”

要创建一个匹配所有空格情况的模式，需要类似这样的复杂内容：/[\t\n\r]/。不过，有一种更简单的方法，它涉及使用 \s（小写s）：

```
function containsWhitespace(string) {
  	return /\s/.test(string);
}

containsWhitespace('Lorem ipsum'); // true
containsWhitespace('Lorem_ips	um'); // true
containsWhitespace('Lorem_ipsum'); // false
```
另外 \S (大写S)可以匹配任何非空白字符。

> #### 指定位置

到目前为止，只是在写单纯可以在字符串中进行匹配的模式。我们还可以指定位置使匹配更精确。

##### 1. 插入符号
如果在模式的开头添加  ^ 符号，则仅当被测试的字符串以该模式开头时，它才会匹配：

```
/^dog/.test('dog and cat'); // true
/^dog/.test('cat and dog'); // false
```
请注意，插入符号用在方括号中时有另外的作用，在上一篇`1 - 基本概念`文章中曾说过。

##### 2. 美元符号
在模式的末尾添加一个美元符号，仅当它出现在字符串的末尾时，才会匹配：

```
/dog$/.test('dog and cat'); // false
/dog$/.test('cat and dog'); // true
```

```
const expression = /^([0-9]+)*$/;
expression.test('123456789!'); // false
expression.test('123456789'); // true
```

##### 3. 结合两个标志
如果你的模式以 ^ 开头，并以 $ 结尾，则仅当测试的字符串整体匹配时，它才会匹配：

```
/success/.test('Unsuccessful operation'); // true
/^success$/.test('Unsuccessful operation'); // false
```
即使在测试的字符串中可以找到字符串 “success”，将模式包含在 ^ 和 $ 中也会使它仅在整个字符串匹配时才匹配。

再看一个例子：

```
function areAllCharactersDigits(string) {
  return /^[0-9]+$/.test(string);
}
```
这个例子检查字符串是否仅包含数字。使用加号会使它匹配一位或多位数字。如果在字符串的开头到结尾之间有数字，并且没有其他内容，则将模式用 ^ 和 $ 括起来能够确保仅匹配表达式。

```
areAllCharactersDigits('123'); // true
areAllCharactersDigits('Digits: 123'); // false
```

下面的模式能够匹配第二个字符串：

```
/[0-9]+/.test('Digits: 123');  // true
```


##### 4. 多行模式
我们已经了解到可以将其他标志添加到模式中。其中之一是由字母 m 表示的多行标志。它改变了插入符号和美元符号的含义。在多行模式下，它们代表一行的开头和结尾，而不是整个字符串。

```
const pets = `
dog
cat
parrot and other birds
`;

/^dog$/m.test(pets); // true
/^cat$/m.test(pets); // true
/^parrot$/m.test(pets); // false
```

我在这里用了模板字符串添加换行符。还可以这样做：

```
const pets = 'dog\ncat\nparrot and other birds';

/^dog$/m.test(pets); // true
/^cat$/m.test(pets); // true
/^parrot$/m.test(pets); // false
```
由于使用了多行标志，因此是测试了多个行，而不测试了整个字符串。但是你会发现最后的测试仍然无法通过，因为最后一行包含的内容不只是“parrot”。

> #### 总结

这次，我们学习了更多的特殊字符，并通过它们用较短的形式编写更复杂的模式。现在你更加了解了匹配模式，从而进一步了解如何指定要查找的样式的位置：字符串的开头与结尾，能够写出能够匹配整个字符串或行（多行模式下）的正则表达式。我们写出的模式将会越来越复杂：我鼓励你多去使用。后续的文章即将推出，请持续关注！
