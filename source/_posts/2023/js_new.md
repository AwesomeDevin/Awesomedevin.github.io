---
title: 彻底捋清楚javascript中 new 运算符的实现 
date: 2021-04-20
categories:
- javascript
tags:
- javascript 基础
---


## new 运算符
>在js中,`new()`常被用来创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例
#### new 关键字会进行如下的操作：
>1.创建一个简单`JavaScript空对象`（即{}）；  
2.链接该对象到另一个对象 （即设置该对象的`_proto_`为构造函数的`prototype`）；  
3.执行构造函数,将构造函数内的this作用域指向`1步骤`中创建的空对象{}；  
4.如果构造函数有返回值，则return `返回值`,否则return `空对象obj`。

## 重点解析:实现一个new
1.我们先来写一个`构造函数`
```javascript
function Constructor(obj){
  if(obj){
    return obj
  }
}
```
2.使用new得到的实例化对象并查看它的__proto__
```javascript
console.log(new Constructor(123))
console.log(new Constructor({a:123}))
console.log((new Constructor()).__proto__)
```
![](https://user-gold-cdn.xitu.io/2019/4/28/16a62f29561acb2d?w=326&h=103&f=png&s=2840)
可以看到，参数为非对象时，会返回{},参数为对象，返回object

3.现在我们来尝试实现一个new()
```javascript
function subNew(){
  var obj = {}
  obj.__proto__ = Constructor.prototype
  var res = Constructor.call(obj,...arguments)
  return typeof(res) === 'object'&&res || obj  
//当构造函数有return时，返回return值,没有则返回obj{}
}
```
4.使用 `subNew()` 创建实例并对比`new()`
```javascript
var obj1 = new Constructor()
var obj2 = subNew()
console.log(new Constructor(123),subNew(123))
console.log(new Constructor({a:123}),subNew({a:123}))
console.log(obj1.__proto__,obj2.__proto__,Constructor.prototype)
```
![](https://user-gold-cdn.xitu.io/2019/4/28/16a62f27bc15cadc?w=548&h=193&f=png&s=9556)
可以发现 我们已经成功创建了new的实例,并且obj1,obj2的__proto__都指向Constructor的prototype

### 5.[使用subNew尝试实现JS继承](https://github.com/AwesomeDevin/blog/issues/8)

## 总结
### new 创建了一个对象obj,并将obj的隐式原型`__proto__`指向构造函数的原型`prototype`,同时使用`call`调用父级的构造函数并传参,之后判断call返回值res，若为object类型，则返回res，否则返回obj

[如果觉得我的文章对你有帮助，请star我的Blog](https://github.com/AwesomeDevin/blog)