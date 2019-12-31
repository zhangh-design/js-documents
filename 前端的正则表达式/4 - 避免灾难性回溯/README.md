## 避免灾难性回溯

![image](https://github.com/zhangh-design/js-documents/raw/master/%E5%89%8D%E7%AB%AF%E7%9A%84%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F/4%20-%20%E9%81%BF%E5%85%8D%E7%81%BE%E9%9A%BE%E6%80%A7%E5%9B%9E%E6%BA%AF/1.jpg)

> 前言：正则表达式可以解决许多问题，但也有可能是使我们头痛的根源。在本文中，我们将会学习需要注意的情况，例如灾难性的回溯。为了帮助我们理解问题，还分析了贪婪和懒惰量词以及为什么 lookahead 可能会有所帮助。


> #### 深入研究量词

正则表达式引擎非常复杂。尽管我们可以用 regexp 创造奇迹，但需要考虑可能会遇到的一些问题。所以需要更深入地研究如何去执行某些正则表达式。

##### 1. 贪婪量词

在本系列文章的前几部分中，我们使用了 `+` 之类的量词。它告诉引擎至少匹配一个。

```
const expression = /e+/;

expression.test('Hello!'); // true
expression.test('Heeeeello!'); // true
expression.test('Hllo!'); // false
```
让我们仔细看看第二个例子：/e+/.test('Heeeeello!')。我们可能想知道用这个表达式匹配多少个字母。

由于默认情况下量词是贪婪的，因此我们会匹配尽可能多的字母。可以用 match函数来确认这一点。

```
'Heeeeello!'.match(/e+/);
// ["eeeee", index: 1, input: "Heeeeello!", groups: undefined]
```

另一个不错的例子是处理一些 HTML 标签：

```
const string = 'Beware of <strong>greedy</strong> quantifiers!';
/<.+>/.test(string); // true
```

最初的猜测可能是它与`<strong>`之类的东西匹配。不完全是！

```
string.match(/<.+>/);
// ["<strong>greedy</strong>" (...) ]

. 匹配除了换行符以外的任何单个字符
+ 至少匹配一次
```

如你所见，贪婪的量词与最长的字符串匹配！


##### 2. 惰性量词

在本系列中，我们还将介绍`?`量词。这意味着匹配零或一次。


```
function wereFilesFound(string) {
  return /[1-9][0-9]* files? found/.test(string);
}

wereFilesFound('0 files found');  // false
wereFilesFound('No files found'); // false
wereFilesFound('1 file found');   // true
wereFilesFound('2 files found');  // true

? 表示重复前面内容的0次或一次
```

有趣的是，通过将其添加到贪婪的量词中，我们告诉它重复尽可能少的次数，因此使其变得懒惰。

```
const string = 'Beware of <strong>greedy</strong> quantifiers!';
string.match(/<.+?>/);

// ["<strong>", (...) ]
```




> #### 灾难性的回溯

要了解量词如何影响正则表达式的行为，我们需要仔细研究被称为回溯的过程。

先让我们看一下这段看似清白的代码！

```
const expression = /^([0-9]+)*$/;
```
乍一看，它可以成功检测到一系列数字。让我们分解一下它的工作方式。

```
expression.test('123456789!');
```
- 首先，引擎处理 [0-9]+。它是贪婪的，所以它会首先尝试匹配尽可能多的数字。首先匹配的是 123456789
- 然后引擎尝试应用 * 量词，但没有其他数字了
- 因为用的是 $ 符号，所以我们希望字符串以数字结尾—— ! 符号不会发生这种情况
- 现在由于回溯，在 [[0-9]+] 中匹配的字符数量减少了。它匹配 12345678。
- 然后使用 * 量词，因此  ([0-9]+)*  产生两个子字符串：12345678 和 9
- 由于上述子字符串均不在字符串末尾，因此与 $ 匹配失败
- 引擎通过减少 [0-9]+ 匹配的位数来保持回溯

上述过程会产生多种不同的组合。

> #### 先行断言（Lookahead）

要解决上述问题，最直接方法是完全重写正则表达式。上面的解决方案并不总是很容易，而且有可能会造成很大的痛苦。解决上述问题的方法是使用先行断言（lookahead）。

在最基本的形式中，它声明 x 仅会在其后跟随 y 时才匹配。

```
const expression = /x(?=y)/;

expression.test('x'); // false
expression.test('xy'); // true
expression.test('bbbxy'); // true
```

我们将其称为正向先行断言。仅当 x 后面不跟随 y 时，用负向先行断言匹配 x

```
const expression = /x(?!y)/;

expression.test('x'); // true
expression.test('xy'); // false
```
先行断言很酷的地方在于它是原子性的。在满足条件后，引擎将不会回溯并尝试其他排列。


> #### 回溯引用（Backreference）

我们在这里需要涉及到的另一个问题是回溯引用。

```
const expression = /(a|b)(c|d)\1\2/;
```
上面的 \1 表示第一个捕获组的内容，而 \2 表示第二个捕获组的内容。

```
expression.test('acac'); // true
expression.test('adad'); // true
expression.test('bcbc'); // true
expression.test('bdbd'); // true

expression.test('abcd'); // false
```
我们可以结合使用先行断言和回溯引用来处理回溯问题：

```
const expression = /^(?=([0-9]+))\1*$/
```

这看起来很复杂。让我们对它进行分解。
- 表达式 (?=([0-9]+)) 寻找最长的数字字符串，因为 + 是贪婪的
- 引擎不会回溯寻找不同的组合
- 表达式 (?=([0-9]+))\1  的回溯引用指出，先行查找的内容需要出现在字符串中


由于上述所有原因，我们可以安全地测试很长的字符串，而不会产生性能问题。

```
const expression = /^(?=([0-9]+))\1*$/;
expression.test('5342193376141170558801674478263705216832 D:'); //false
expression.test('7558004377221767420519835955607645787848'); // true
```

> #### 总结

在本文中，我们更深入地研究了量词。可以将它们分为贪婪和懒惰两种量词，并且它们可能会对性能产生影响。我们还讨论了量词可能导致的另一个问题：灾难性回溯。我们还学习了如何使用 先行断言（lookahead） 来改善性能，而不仅仅是去重写表达式。有了这些知识，我们可以编写更好的代码，避免出现Cloudflare这样的问题。
