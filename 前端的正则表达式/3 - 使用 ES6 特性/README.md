## 使用 ES6 特性

> 前言：到现在为止，我们已经介绍了许多正则表达式的功能。但是还有更多。这次我们将会学习一些更高级的概念，例如搜索和覆盖 JavaScript 中 RegExp 对象的更多功能。我们还将学习如何使用 ES6 带来的一些功能。开始吧！


> #### exec

这是一种执行搜索字符串中的匹配项的方法（类似于 test 方法），但是它返回的结果是数组（或 null）。其结果还有其他一些属性，例如 index 和 input

```
const string = 'fileName.png, fileName2.png, fileName3.png hello';
const regexp = /fileName[0-9]?.png/g;

regexp.exec(string);

[
  0: "fileName.png",
  index: 0,
  input: "fileName.png, fileName2.png, fileName3.png hello"
]
```
index 是匹配项的位置，input 是提供的字符串。请注意，我在这里用的是 global 标志，在课程的第一部分`1 - 基本概念`中已提到过。所以我们可以通过多次调用 exec 在字符串中寻找多个匹配项。它将 RegExp 对象的 lastIndex 属性设置为一个数字，该数字指示搜索停止的位置。

```
let resultArray;
while((resultArray = regexp.exec(string)) !== null) {
  	console.log(resultArray[0], regexp.lastIndex);
}

// fileName.png  12
// fileName2.png 27
// fileName3.png 42
```

> #### 正则表达式中的分组

使用正则表达式，不仅可以检查字符串是否匹配，还可以在忽略不必要字符的同时提取某些信息。可以使用带有圆括号的分组。

```
function getDateFromString(dateString) {
  const regexp = /([0-9]{2})-([0-9]{2})-([0-9]{4})/;
  const result = regexp.exec(dateString);
  if(result) {
    return {
      day: result[1],
      month: result[2],
      year: result[3]
    }
  }
}

getDateFromString('14-05-2018');
{
  day: '14',
  month: '05',
  year: '2018'
}
```
在这种情况下，我们提取了三组字符，而忽略了破折号。只需注意 result [0] 将是匹配的完整字符串。


```
const string = 'fileName.png, fileName2.png, fileName3.png hello';
const regexp = /(fileName[0-9]?.png), (fileName[0-9]?.png), (fileName[0-9]?.png)/;

const result = regexp.exec(string)
result[0] // fileName.png, fileName2.png, fileName3.png
result[1] // fileName.png
result[2] // fileName2.png
result[3] // fileName3.png
```
regexp 匹配模式写三个就匹配三个，写两个就匹配两个。


##### 1. 嵌套分组

你可以嵌套分组：

```
function getYearFromString(dateString) {
  	const regexp = /[0-9]{2}-[0-9]{2}-([0-9]{2}([0-9]{2}))/;
  	const result = regexp.exec(dateString);
  	console.info(result);
  	if(result) {
	    return {
	      year: result[1],
	      yearShort: result[2]
	    }
  	}
}
getYearFromString('14-05-2018');
{
  year: '2018',
  yearShort: '18'
}
```
在模式的 ([0-9]{2}([0-9]{2})) 部分中，我们将一组嵌套在另一组中。多亏了这一点，我们得到了表示年份的短字符串。

##### 2. 条件模式

还有另一个有用的功能，即 OR 语句。我们可以用 | 字符实现：

```
function doYearsMatch(firstDateString, secondDateString) {
   const execResult = /[0-9]{2}-[0-9]{2}-([0-9]{4})/.exec(firstDateString);
   if(execResult) {
     const year = execResult[1];
     const yearShort = year.substr(2,4);
     return RegExp(`[0-9]{2}-[0-9]{2}-(${year}|${yearShort})`).test(secondDateString);
   }
}

doYearsMatch('14-05-2018', '12-02-2018'); // true
doYearsMatch('14-05-2018', '24-04-18');   // true
```
按照我们的模式，(${year}|${yearShort}) 将会匹配年份，即使第二个年份以简短形式提供。


##### 3. 全部捕获

与组一起工作时，还有一个特别有用的地方：(. *)

这里的 `?` 表示匹配前面的空格 0次或一次 
```
function getResolution(resolutionString) {
  const execResult = /(.*) ?x ?(.*)/.exec(resolutionString);
  if(execResult) {
    return {
      width: execResult[1],
      height: execResult[2] 
    }
  }
}
getResolution('1024x768'); 
{
  width: '1024',
  height: '768'
}
```
多亏用了 `?` 运算符，如果还有其他空格，它也将起作用：

```
getResolution('1920 x 1080');

{
  width: '1920',
  height: '1080'
}
```


> #### 粘性标志（sticky）

如你所见，RegExp 对象有一个名为`lastIndex`的属性。当进行全局搜索（使用适当的标志）时，可以在正确的位置继续进行模式匹配。

使用 ES6 中引入的 粘性标志 `y`，我们可以强制从某个索引开始搜索。

```
function getDateFromString(dateString) {
  	const regexp = /([0-9]{2})-([0-9]{2})-([0-9]{4})/y;
  	regexp.lastIndex = 14;
  	const result = regexp.exec(dateString);
  	if(result){
	    return {
	      day: result[1],
	      month: result[2],
	      year: result[3]
	    }
  	}
}
getDateFromString('Current date: 14-05-2018');
```
请记住，对字符串执行检查（例如使用 exec）会更改 lastIndex 属性，所以如果你希望它在多次粘性搜索之间保持不变，请不要忘记对其进行设置。如果模式匹配失败，则将 lastIndex 设置为0。

注意：你可以检查 RegExp 对象是否启用了标志。

```
const regexp = /([0-9]{2})-([0-9]{2})-([0-9]{4})/y;
regexp.lastIndex = 14;
console.log(regexp.sticky); // true
```


> #### Unicode 标志

ES6 也带来了对 Unicode 的更好支持。添加 Unicode 标志 u 可以启用与 Unicode 相关的其他功能。多亏了它，你可以在模式中使用 \u{x}，其中 x 是所需字符的编码。

```
/\u{24}/u.test('$'); // true
```
以上代码如果没有`u`标志将会无法工作。重要的是要知道它的影响不仅限于此。如果不带标志，也可以处理一些的更特殊的 Unicode 字符：

```
/😹/.test('😹'); // true
```
但这会在更复杂的情况下使我们失败：

```
/a.b/.test('a😹b');  // false
/a.b/u.test('a😹b'); // true

/😹{2}/.test('😹😹');  // false
/😹{2}/u.test('😹😹'); // true

/[a😹b]/.test('😹');  // false
/[😹🐶]/u.test('😹'); // true

/^[^x]$/.test('😹');  // false
/[^x]/.test('😹');    // true
/^[^x]$/u.test('😹'); // true

/^[ab😹]$/.test('😹');  // false
/[ab😹]/.test('😹');    // true
/^[ab😹]$/u.test('😹'); // true
```
我们可以很容易地得出一个结论，那就是在模式中包含`u`标志是一种很好的做法，尤其是再可能含有除了标准 `ASCII` 之外的其他字符的情况。


如果将它与 忽略大小写 标志结合使用，则该模式也将同时匹配小写和大写字符。

```
/\u{78}/ui.test('X'); // true
```


> #### 总结

今天，我们了解了有关 JavaScript 中的 RegExp 对象的更多信息，以及如何通过正则表达式的一个强大功能来运用这个知识：分组。我们还学习了两个新标记：粘性和 Unicode。下次再见！
