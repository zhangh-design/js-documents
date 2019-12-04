## 执行环境及作用域

1. **this的定义**
> `this`就是函数赖以执行的对象。

分析这句话：
1. `this`是对象。
2. `this`依赖函数执行的上下文环境。
3. `this`存在函数中。

> 示例：

```
// 在全局环境调用this, this指向window,  输出[Object window]
alert(this);

function Person(){
    alert(this);
}
// 方式一：

// 全局环境用Person函数, this指向window,  输出[Object window]
Person();

// 方式二：

/**
* 把Person当做构造函数, 实例化一个对象
* 此时this指向了obj, 不再指向window,  输出[Object object]
*/
var obj = new Person();

function Person(){
    // //此时无法判断this的身份
    alert(this.name);
}
// this在全局环境中被调用，this.name == window.name，输出了窗口的名字
Person();
// this在obj环境下被调用, this.name == obj.name, 由于name没被赋值, 所以输出undefined
var obj = new Person();
```

**结语：**

由此可以看出，我们在阅读代码或者写代码时，看到某个函数中定义的`this`时, 还无法去判断那个`this`身份，必须找到它依赖执行的环境（对象）。

2. **constructor构造函数**
> `constructor`是一个对象的属性，这个属性存在在此对象的`prototype`中，指向此对象的构造函数。

分析这句话：
1. `constructor`是一个对象属性。
2. `constructor`在`prototype`中。
3. `constructor`指向构造函数。

> 示例1：


```
function Person(name, age){
    this.name = name;
    this.age = age;
}
Person.prototype.getName = function(){
    alert(this.name);
}
Person.prototype.getAge = function(){
    alert(this.age);
}
var obj = new Person();
alert(obj.constructor == Person); // true
// getName和getAge上面两种方式定义的prototype, constructor是隐藏的, 默认指向Person。

```
上面示例重点输出讲解：

```
console.info(Person);
// 输出构造函数本身
ƒ Person(name, age){
    this.name = name;
    this.age = age;
}
```

```
console.info(Person.constructor);
// 输出二进制编译的本地代码
ƒ Function() { [native code] }
```

```
console.info(Person.prototype);
```
输出构造函数的原型对象并没有实例对象上的`name`和`age`属性，其中就有`constructor`：

![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/1.png)

```
console.info(obj);
```
输出实例属性和原型对象都有：

![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/2.png)

```
console.info(obj.constructor);
// 输出对象实例的构造函数：
ƒ Person(name, age){
    this.name = name;
    this.age = age;
}
```


```
console.info(obj.constructor == Person);
// true
```

```
console.info(obj.prototype);
// 对象实例上并没有`prototype`属性但提供了__proto__这两者的区别后面会详细讲到
// undefined
```

```
console.info(obj.__proto__);
```
![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/1.png)

```
Person.prototype.constructor
构造函数原型的构造函数还是 Person 函数本身
输出：
ƒ Person(name, age){
    this.name = name;
    this.age = age;
    this.getAddress = function(){
        return '西湖区西斗门路3号';
    }
}
```

> 示例二：

```
function Person(name, age){
    this.name = name;
    this.age = age;
}
Person.prototype = {
    getName: function(){
       alert(this.name);
    },
    getAge: function(){
       alert(this.age);
    }
}
var obj = new Person();
console.info(obj.constructor == Person);
// false
console.info(obj.constructor == Object);
// true
```
示例1中的 `obj.constructor == Person`是等于`true`但示例2中的`obj.constructor == Person`却等于了`false`那为什么是`false`？这种定义`prototype`, 是把`prototype`重写了,覆盖了默认的`constructor`。换句话说,其实这种方式就是给属性重新赋值了, 所以导致默认的`constructor`被覆盖，此时的obj.constructor将指向的是Object。。

修改示例二：

```
Person.prototype = {
    // 强制指向Person
	constructor: Person,
    getName: function(){
       alert(this.name);
    },
    getAge: function(){
       alert(this.age);
    }
}
var obj = new Person();
console.info(obj.constructor == Person);
// true
console.info(obj.constructor == Object);
// false
```
此时`constructor`就指向`Person`了。

3. **prototype原型对象**
> `prototype`是一个函数属性，此属性同时也是一个对象，保存着对象实例所共有的属性和方法。

分析这句话：
1. `prototype`是函数属性，只要是函数就有`prototype`属性，而不管是构造函数还是普通函数。
2. `prototype`同时也是对象。
3. `prototype`放的是公共的东西, 包括属性和方法。

构造函数：
> function 定义的函数。

普通函数：
> {}

![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/3.png)

> 示例一：

```
function Person(name, age){
    this.name = name;
    this.age = age;
}
Person.prototype.getName = function(){
    alert(this.name);
}
Person.prototype.getAge = function(){
    alert(this.age);
}
var obj = new Person('tom', 23);
obj.getName(); //'tom'
var obj2 = new Person('jack', 23);
obj2.getName(); //'jack'
console.info(obj.getName() === obj2.getName());
// true 所有实例共享
// obj和obj2都指向同一个prototype对象也就是说prototype对象存放的是公共的东西。

Person.prototype.getName();
// 当做普通函数属性, 根据this定义, 此时this指向的是Person.prototype, 所以返回undefined
// undefined 
```

> 示例二：

```
window.name="小明"
function Person(name, age){
    this.name = name;
    this.age = age;
    this.getAddress = function(){
    	console.info('西湖区西斗门路3号');
    }
}
Person.prototype.name="小孔"
Person.prototype.getName = function(){
    console.info(this.name);
}
Person.prototype.getAge = function(){
    console.info(this.age);
}
var obj = new Person('小米',17);
var P = obj.constructor;
console.info(obj.name);	// 小米
console.info(Person.name, Person.age);// Person undefined
console.info(Person.prototype.name);// 小孔
console.info(obj.getAddress());// 西湖区西斗门路3号
console.info(obj.getName());// 小米
console.info(obj.__proto__.getName());// 小孔
console.info(P === Person);// true
```

4. **`__proto__`属性**

`__proto__`属性其实是指向了函数的父对象，`prototype`属性其实是指向了函数本身的原型实例。

注意点：但是由于`JS`中函数也是一种对象，所以函数也拥有`__proto__`和`constructor`属性，这点是致使我们产生困惑的很大原因之一，我们看下图片：

1. `__proto__`和`constructor`属性是对象所独有的。
2. `prototype`属性是函数所独有的。

![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/4.png)

```
console.info(Person.constructor); //ƒ () { [native code] }
console.info(Person.__proto__);   //ƒ () { [native code] }
```

```
console.info(obj.__proto__);
console.info(Person.prototype);
```
![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/5.png)

注意点：

这里我们仅留下 `__proto__` 属性，它是对象所独有的，可以看到`__proto__`属性都是由一个对象指向一个对象，即指向它们的原型对象（也可以理解为父对象），那么这个属性的作用是什么呢？它的作用就是当访问一个对象的属性时，如果该对象内部不存在这个属性，那么就会去它的`__proto__`属性所指向的那个对象（可以理解为父对象）里找，如果父对象也不存在这个属性，则继续往父对象的`__proto__`属性所指向的那个对象（可以理解为爷爷对象）里找，如果还没找到，则继续往上找。


![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/6.png)

![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/7.png)

> 示例分析1：

> 使用下面的方法会将 `method` 方法挂载到任意一个创建的函数中

```
Function.prototype.method = function (name, fn) {
	if(!this.prototype[name]){
		// this指向构造函数本身
		this.prototype[name] = fn
	}
	return this
}
```

> this.constructor返回[Native code]形式的调用执行：

注意：

1：类似`Promise`这些js底层提供的函数它的构造函数返回是 `ƒ Promise() { [native code] }`，`native code`与正常的方法执行不同的是，`native code`是计算机二进制并不是JS代码，是浏览器对`Promise`方法的实现，可能是用C++代码编写的，无法通过`()`来直接执行，只有通过抛出的接口来调用。

2：那正常我们自己创建的函数例如：`var b = function(){var name='小米';}` 那它的构造函数输出`（console.info(new b().constructor)）`应该是函数本身 `ƒ (){var name='小米';}`。

![image](https://raw.githubusercontent.com/zhangh-design/js-documents/master/%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%8F%8A%E4%BD%9C%E7%94%A8%E5%9F%9F/8.png)

```
Promise.prototype.finally = function (callback){
	// P指向构造函数本身
	// 这里如果想要操作 resolve 函数需要使用构造函数
	// 这里需要注意Premise.constructor构造函数返回的是 [Native code]所以才能使用P.resolve ，resolve是底层[Native code]提供的接口和一般定义的函数不同
	let P = this.constructor
	return this.then(
		value => P.resolve(callback()).then(() => value),
		reason => P.resolve(callback()).then(() => {throw reason})
	)
}
```


