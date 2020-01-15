```
var slice = [].slice;
// def 默认值
var _get = function(source, scope, def) {
	var key, keys, value, values;
	keys = [];
	values = [];
	for (key in scope) {
		// 自身属性过滤继承的
		if (!Object.prototype.hasOwnProperty.call(scope, key)) continue;
		value = scope[key];
		if (key === 'this') {
			continue;
		}
		keys.push(key);
		values.push(value);
	}
	// console.info(keys, values);
	// console.info(Function.apply(null, slice.call(keys).concat(["return eval(" + (JSON.stringify(source)) + ")"])));
	return Function.apply(null, slice.call(keys).concat(["return eval(" + (JSON.stringify(source)) + ")|| "+def+""])).apply(scope["this"], values);
};
var text = bb("c.a", {a: 1,b: 2,c: {a:31}}, 100)
var text1 = bb("c.b.m", {a: 1,b: 2,c: {a:31}}, 100)
console.info(text); // 31
console.info(text1); // 100
```