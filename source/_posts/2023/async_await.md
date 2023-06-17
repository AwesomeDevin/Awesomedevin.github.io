---
title: 深入理解async/await的实现,Generator+Promise = Async/Await 
date: 2019-04-28
index: 1
categories:
- javascript
tag:
- javascript 基础
---

## 概念
>Generator 函数是 ES6 提供的一种异步编程解决方案，执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

##  yield表达式
>形式上，Generator 函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；二是，函数体内部使用<font color="red">yield</font>表达式，定义不同的内部状态。
```
function* foo(x) {
    yield 1
    yield 2
    yield 3
    yield 4
    return 5
}
```

>必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用<font color="red">next</font>方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。
```
function* foo() {
    yield 1
    yield 2
    return 3
}

var f = foo()
f.next()     //{ value: 1, done: false }
f.next().value     //2
f.next()     //{ value: 3, done: true}
f.next()     //{ value: undefined, done: true}
```
>Generator 函数已经运行完毕，next方法返回对象的value属性为3，done属性为true，之后再执行next(),done都为true,value未undefined

## next方法的参数
>yield表达式本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。
```javascript
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```
##  Generator + Promise = 强大的异步回调方式
##### 没有回调    
```javascript
function sayhello() {
    setTimeout(()=>{
        console.log(123)
    },3000)
}

function helloworld() {
    const data =  sayhello();
    console.log(data);
    console.log(456)
}
helloworld()
```
![这里写图片描述](https://user-gold-cdn.xitu.io/2019/4/19/16a3465bbfa317bc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### 使用async/await 
```javascript
function sayhello(){
	return new Promise((resolve)=>{
		setTimeout(()=>{
			resolve(123)
	        console.log(123)
	    },3000)
	})
}

async helloworld(){
    const data = await sayhello()
    console.log(data)
    console.log(456)
}
```
##### 使用Generator + Promise  实现async/await
```javascript
function co(gen) {
    if(!gen) return
	return new Promise((resolve,reject)=>{
		var it = gen();
		try{
			function step(next){
				if(next.done)
				{
					return resolve(next.value)
				}
				else{
					Promise.resolve(next.value).then((res)=>{
						return step(it.next(res))
					},(e)=>{
						return step(it.throw(e))
					})
				}
			}
			step(it.next())
		}
		catch(e){
			return reject(e)
		}
	})
}

function sayhello(){
       // 之所以需要返回一个 promise ,是因为 co 函数会在底层使用 promise 同步执行 sayhello ，为了保证 sayhello 同步执行完该异步函数后获取到正确的返回值，需要使用promise包裹返回值。
	return new Promise((resolve)=>{
		setTimeout(()=>{
			resolve(123)
	        console.log(123)
	    },3000)
	})
}

co(
	function* helloworld(){
		const data = yield sayhello()
		console.log(data)
		console.log(456)
	}
)
```
![这里写图片描述](https://user-gold-cdn.xitu.io/2019/4/19/16a3465bbff1005c?w=533&h=65&f=png&s=2258)

### 可以看到，通过Generator + Promise(async/await)我们已经拿到了延时器中的数据。
### 任何复杂的异步功能都可以被promise搞定，而且你还可以用generator把这些流程写的像同步代码一样。只要你让yield返回一个promise。