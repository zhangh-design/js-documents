## jsDoc代码注释风格

资料连接：
https://blog.csdn.net/weixin_33991727/article/details/89572268
	
http://www.ayqy.net/blog/jsdoc%E6%94%AF%E6%8C%81_typescript%E7%AC%94%E8%AE%B019/

`jsDoc`注释语法结合 `jsconfig.json` 配置文件在项目中以静态方式验证代码适用于`.js`文件（主要是用于.js的插件开发），主要是为了使用`jsDoc`后期通过`jsdoc`的命令来生成说明文档用的（注释即文档这样插件写完了文档也就出来了），如果说是用于代码静态检测那么我觉得 `Flow` 可能会更加合适。

#### 类注释


```
/**
 * @class 类名
 * @classdesc 类的简单描述
 * @desc 类的详细说明
 * @see 插件功能详细介绍请查看
 * {@link https://说明文档连接 点击前往}
 * @author 作者
 * @version 版本
 * @example
 * 示例
 */

const myTest = class Person {}

```

#### 变量

@type {object} 表示：变量类型，类型可以是string、number、boolean、function、object、any、*、this。

```
/**
 * @description
 * 变量的详细描述
 * @access public （指定该成员的访问级别（私有 private，公共 public，或保护 protected））
 * @type {object} 类型
 * @readonly 标记是否为只读的
 * @default 记录默认值
 * @example
 * 示例
 * */

this.api = {}
```

#### 对象变量

@type {object} 表示：变量的类型

@property {string} mockBasePath 表示：变量内部值的说明

mock=false 表示：值的默认值是false
```
/**
 * @desc
 * 变量说明
 * @type {object}
 * @property {string} mockBasePath 参数说明
 * @property {boolean} mock=false 参数说明
 */
const apiDefaultConfig = {}
```

#### 普通函数

{string} 表示：参数是字符类型

[defaultValue=null] 表示：[]参数可选，=null默认值是null
```
/**
  * @desc 接口函数说明
  * @param {string} key - 参数说明
  * @param {object} [defaultValue=null] - 参数说明
  * @example
  * 示例
  */
getValue (key = '', defaultValue = null) {}
```

#### 可变参函数

{...*} 表示：...是可变参数， *代表任意类型

```
/**
   * @desc
   * 接口函数说明
   * @param {string} tag='' - 参数说明
   * @param {...*} msg  - 参数说明
   * @example
   * 示例
   */
v (tag = '', ...msg) {}
```

#### 作用域参数函数

{this} 表示：参数是实例对象

{function} 表示：参数是一个函数

{...any} 表示：...是可变参数，any代表任意类型

@returns {number} 表示：函数返回值的类型是数字类型

```
/**
   * @desc 接口函数说明
   * @param {this} scope - 参数说明
   * @param {function} handler - 参数说明
   * @param {number} interval - 参数说明
   * @param  {...any} [params] - 参数说明
   * @returns {number}
   * @example
   * 示例
   *
   */
setInterval (scope = null, handler = function () {}, interval = 1000, ...params) {}
```


#### 自定义类型

@typedef 表示：可以声明一个自定义的注释类型

@param {options} 表示：使用自定义类型 options
```
/**
   * @typedef {Object} options - 选项配置对象
   * @property {boolean} leading - 指定在延迟开始前调用
   * @property {boolean} trailing - 指定在延迟结束后调用
   */
  /**
   * @desc 节流定时器，在指定时间后执行
   * @param {this} scope - 提供给 handler 参数的作用域
   * @param {function} handler - 执行函数
   * @param {number} wait - 需要延迟的毫秒数
   * @param {options} [options={leading: false, trailing: true}] - 选项配置
   * @returns {function} 返回新的 debounced （防抖动）函数，可以用于取消防抖动调用
   * @example
   * let doSize = function () {console.info('resize');}
   * var myDebounce = tp.setThrottle(personInstance, doSize, 3000)
   * jQuery(window).resize(myDebounce)
   */
setThrottle (scope = null, handler = null, wait = 1000, options = { leading: false, trailing: true }) {}   

```

#### 自定义类型（外部.js文件单独定义）

JSDoc 中采用 ES Module 引入语法

b.js
```
/**
 * @typedef {Object} options - 选项配置对象
 * @property {boolean} leading - 指定在延迟开始前调用
 * @property {boolean} trailing - 指定在延迟结束后调用
 */
```

index.js

```
import * as A from './b.js';

/**
 * @param {A.options} [options={leading: false, trailing: true}] - 选项配置
 */

```
