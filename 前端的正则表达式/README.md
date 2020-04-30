
目录 | 说明
---|---
1 | 基本概念
2 | 写出更优雅、更精确的正则表达式
3 | 使用 ES6 特性
4 | 避免灾难性回溯

```
[ ] 这个模式用来匹配一个字符，该字符可能是括号中的任何字符。

. 用来匹配除了换行符以外的任何单个字符。

[^] ^ 符号轻松获得否定字符集。它会匹配方括号中未包含的所有内容。

i：忽略大小写 （使用这个标志，搜索时不区分大小写）

g：全局匹配 （多亏了这个标志，所有匹配项都能够被找到。如果没有它，将会在找到第一个匹配项后停止。）

* 匹配 0 次或更多次。它实际上等效于 {0,}

? {0,1} （问号可以表示重复前面内容的0次或一次，也就是要么不出现，要么出现一次。）

+ {1,} （表示该表达式可能匹配一次或多次。这类似于星号，但在这里必须至少匹配一次。）

{ } 匹配某个表达式出现的确切次数。
1. {x} 完全匹配 x 次出现
2. {x,} 至少匹配 x 次
3. {x,y} 至少匹配 x 次且不超过 y 次

() 是为了提取匹配的字符串。表达式中有几个()就有几个相应的匹配字符串。

```

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


// 前瞻：
exp1(?=exp2) 查找exp2前面的exp1
// 后顾：
(?<=exp2)exp1 查找exp2后面的exp1
// 负前瞻：
exp1(?!exp2) 查找后面不是exp2的exp1
// 负后顾：
(?<!exp2)exp1 查找前面不是exp2的exp1

它声明 x 仅会在其后跟随 y 时才匹配。
```
const expression = /x(?=y)/;

expression.test('x'); // false
expression.test('xy'); // true
expression.test('bbbxy'); // true
```

当 x 后面不跟随 y 时，用负向先行断言匹配 x
```
const expression = /x(?!y)/;

expression.test('x'); // true
expression.test('xy'); // false
```

不包含某个字符串：

```
function hasQuestionMarkBeforeEnd(string) {
  				return /^((?!badword).*)\.(png|jpe?g|gif|svg|blob)(\?.*)?$/.test(string);
			}

			var b = hasQuestionMarkBeforeEnd('ba3432.png'); // true
      // var b = hasQuestionMarkBeforeEnd('badword.png'); // false
      // var b = hasQuestionMarkBeforeEnd('badword-23123.png'); // false
			console.info(b);
```
