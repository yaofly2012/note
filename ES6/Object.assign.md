嘛意思：
> This may make it unsuitable for merging new properties into a prototype if the merge sources contain getters
> 

# 功能
执行浅copy，把多个源对象的**可枚举且是自己的**属性**浅复制**给目标对象，并且返回目标对象。如果存在相同名称的属性后面的源对象会覆盖前面的。

# 语法
```javascript
Object.assign(target, ...sources)
```
target: 
1. 是个对象，基本类型会转成对象。但不可以是null, undefined.

source:
1. 是个对象，基本类型会转成对象，忽略null， undefined参数
2. 只copy source自己的且可枚举的属性
3. 只copy source属性的值，不会copy值的特性
	```javascript
	var s = Object.create({}, {b: {
	  value: 1, 
	  writable: false,
	  enumerable: true
	}})

	var t = Object.assign({}, s);
	Object.getOwnPropertyDescriptor(t, 'b') // {value: 1, writable: true, enumerable: true, configurable: true}
	```
4. 如果source包含getter属性，复制到target时也只是获取getter的值
	```javascript
	var s = {
	  get b() {
	    return 1;
	  }
	}
	var t = Object.assign({}, s);
	Object.getOwnPropertyDescriptor(t, 'b') // {value: 1, writable: true, enumerable: true, configurable: true}
	```
**总结一句话**assign方法覆盖或者创建的属性的特性都是true。

# 参考
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)