# Function.prototype.bind
## 1. 功能：
ES6新增对方法
创建一个新对函数，并且指定新增函数的this变量和预设的实参。
## 2. 语法
```javascript
Function bind(this变量, {预设实参1, 预设实参2, ...})
```
1. this变量参数
    指定新函数的this变量，JS的函数调用有多种形式（方法，new方式，函数方式），当新函数以new方式调用时参数this变量是无效的，以new的方式给this变量赋值。
2. 预设实参参数
    给新函数预设的实参，当新函数被调用时这些参数和调用时指定的实参一起传给新函数。可用于[函数柯里化](https://zhuanlan.zhihu.com/p/31271179)
3. 返回值
    新生成的函数，该函数和原函数**具有相同的函数体**，但被指定了this变量和一些预设的实参。

```javascript
var person = {
    name: 'john',
    say: function() {
        console.log(this.name);
    }
}

person.say(); // john
var newSay = person.say.bind({name: 'jack'}); // 通过bind函数创建绑定函数，并指定this变量
newSay(); // jack
person.say = newSay; // 覆盖person对象的say方法
person.say(); // jack
```
通过函数或者方法方式调用绑定函数时无法修改创建绑定函数时指定的this变量。**但是当通过new方式调用绑定函数时就会忽略指定的this变量。**
```javascript
// 任意点构造函数
var Point = function (x ,y) {
    this.x = x;
    this.y = y;
}
Point.prototype.toString =  function() {
    console.log('(' + this.x + ', ' + this.y + ')');
}
new Point(1, 2).toString() // (1, 2)

// 如果表示平行于Y轴，且经过X轴4的直线任意一点，可这样定义：
var Y4Point = Point.bind(void 0, 4)
new Y4Point(2).toString() // (4, 2)
```
在定义Y4Point构造函数时传给给bind函数的this变量参数是undefined，new方式调用Y4Point会忽略该参数，保留以new方式定义this变量。
这样做是有道理的 ：一般构造函数内部会涉及this变量的操作，所以构造函数建议只能通过new方式调用，否则会产生意想不到的结果。当调用构造函数的bind函数产生的绑定函数跟原函数具有相同对函数体，所以新绑定函数也最好只能通过new方式调用，并且忽略指定的this变量，保留new的语义，相当于只保留了bind预设实参的功能了。

```javascript
var emptyObj = {}
var Y4Point = Point.bind(emptyObj, 4); // 指定this变量
Y4Point(2); // 
console.log(emptyObj) // {x: 4, y: 2}
```
通过函数方式调用Y4Point修改了emptyObj对象。
**总结一句话**bind函数功能：
1. 指定函数对this变量（当以new方式调用生成对绑定函数时忽略该参数）;
2. 预设函数部分或者全部实参。
    生成对函数对length对值表示还剩多少个未预设对形参。

## 3. polyfill
对不支持ES6环境需要使用bind的polyfill。基于bind函数的功能来实现个。
1. 基本功能：指定this变量，并且预设置实参
```javascript
// 简单起见不做参数类型判断
var bind = function(func, thisObj) {
  var slice = Array.prototype.slice; // 利用slice方法把arguments转成数组
  var args = slice.call(arguments, 2); // 提取预设的实参
  
  return function() {
  	return func.apply(thisObj, args.concat(slice.call(arguments)));
  }
}
```
2. 当生成的绑定函数通过new方式调用时忽略指定的this参数
在上面的方法基础上修改：
```javascript
var bind = function(func, thisObj) {
  var slice = Array.prototype.slice;
  var args = slice.call(arguments, 2);
  var emptyFunc = function() {}
  emptyFunc.prototype = func.prototype;
    
  var bindFunc = function() {
        // 根据当前执行上下文this是不是bindFunc的实例对象选择执行上下文
  	return func.apply(this instanceof bindFunc ? this : thisObj,  
                args.concat(slice.call(arguments)));
  }
  bindFunc.prototype = new emptyFunc(); // 建立bindFunc创建对象和func.prototype原型链关系
  
  return bindFunc;
}
```


# 参考
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
[unserscore js](https://github.com/jashkenas/underscore/blob/master/underscore.js)