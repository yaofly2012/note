# 一. 语法
从字面上看instanceof运算符用来判断一个对象是否通过一个类（构造函数）创建的。
它的语法格式是：
```javascript
obj instanceof constructorFunc
```
1. 左边表达式必须是个对象，并且不发生封包操作；
2. 右边表达式必须是个函数，否则抛TypeError异常。
```javascript
'a' instanceof String // false
new String('a') instanceof String // true

function Person() {}
var p = new Person();
p instanceof Person; // true
p instanceof Object; // true
```
# 二、剖析
通过new方式创建的对象，等价于一个对象的原型等于构造函数的prototype属性。所以instanceof运算符真正的含义是：
> instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。
```javascript
// 接着上面的Person函数，我们手动创建原型继承关系
var p2 = Object.create(Person.prototype);
p2 instanceof Person; // true
```
三、通过instanceof查看JS常见内置对象的关系
JS中String，Number，Boolean，Date，Function，Object，RegExp即是函数也是对象，并且Object.prototype又是所有对象的顶层原型。他们跟Function，Object都有着关系。
```javascript
// Number，Boolean，Date，RegExp类似
String instanceof Object // true
String instanceof Function // true
String instanceof String; // false

Function instanceof Object // true
Function instanceof Function // true

Object instanceof Object // true
Object instanceof Function // true

Math instanceof Object // true
Math instanceof Function // false, Math是对象，不是函数
```

# 参考
1. [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof)
2. [JavaScript instanceof 运算符深入剖析](https://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/)