# 赵忠洋 ｜ Part 1 | 模块一

## 简答题

### 1.请说出下列最终执行结果，并解释为什么？
```js
    var arr = []
    for (var i = 0; i < 10; i++) {
        arr[i] = function () {
            console.log(i)
        }
    }
    arr[6]()
```
#### 答案
> 输出结果为 10 
#### 简述
> 首先i为全局变量,循环结束之后 i 的值已累加值至10。在函数 arr[6]() 执行时会重新取 i 的值所以 i=10，输出结果为10。通过使用闭包或者let const 都可以解决这个问题。
### 2.请说出下列最终执行结果，并解释为什么？
```js
    var tmp = 123
    if(true){
        console.log(tmp)
        let tmp
    }
```
#### 答案
> 无法正常执行函数报错
#### 简述
> 无法在 let或cosnt 定义变量之前，去访问或修改变量。因为在块级作用域中，let或cons 在声明之前会产生暂时性死区 这个概念并不会 变量提升。如果把 let 改为 var 则输出结果为 123 ，因为执行时 tmp 会先去上层查找是否有 tmp 变量。如果把 let 改为 var 并且 注释最外层 var tmp = 123  则输出结果为 undefined ，因为外部不存在 tmp 的定义 则 if 语句内部的 var tmp 会变量提升 var tmp = undefined。
### 3.结合ES6新语法用最简单的方式找出数组中的最小值
```js
    var arr = [ 11, 34, 32, 89, 4,]
```
#### 答案
```js
    var arr = [ 11, 34, 32, 89, 4,]
    console.log(Math.min(...arr)) // 4
```
### 4.请详细说明 var,let,const 三种声明变量的方式之间的差别？
#### 答案: 
> 在 ES6 以前 JavaScript只有两种作用域，分别是全局作用域与函数作用域。var 在 在块及作用域中 会有变量提升的现象，而 let和const 都是块及作用域 ，不存在变量提升，所以在 let或const 定义之前使用变量 解析器会抛出异常 而不是 undefined。const 与 let 的区别在于 let 定义的值声明后可以修改内存地址，而const 不可以。
### 5.请说出下列最终执行结果，并解释为什么？
```js
var a = 10 ;
var obj = {
    a : 20,
    fn(){
        setTimeout(()=>{
            console.log(this.a)
        })
    }
}
obj.fn()
```
#### 答案: 
> 输出结果为 20
#### 简述
> 箭头函数的this是在定义函数时绑定的，不是在执行过程中绑定的。简单的说，函数在定义时，this就继承了定义函数的对象。可以理解为 fun(){}.bind(this),而fn的this指向的是当前对象，所以 this.a = 20

### 6.简述 Symbol 类型的使用用途
#### 答案: 
> 因为 Symbol具有唯一性 所以可以用于实现私有成员 为对象增加独一无二的属性标识符，让外部无法更改。同时Symbol提供了一些静态方法如 iterator、toStringTag等 这些对象可以实现一些js的接口

### 7.什么是浅拷贝什么是深拷贝
#### 答案: 
> 浅拷贝只会复制引用类型的内存地址而不会重新开辟一个内存空间。而深拷贝会重新开辟一个内存空间存储来存储引用类型的值。

### 8.谈谈你是如何理解Js异步编程的，Event Loop是做什么的 ，什么是微任务 什么是宏任务。
#### 答案: 
> 将耗时的操作异步处理，优化代码性能，异步编程的方案有回调函数模式、Promise、Generator、Async/Await。Event Loop 是消息线程指的是指的是计算机系统的一种运行机制, 负责监听 微任务与宏任务 并且将其放入调用栈。微任务直接在本文执行的末尾执行"微任务"例如 Promise、MutationObserver 。宏任务是指回调队列，例如setTimerout的回调函数，，在当前的微任务没有执行完成时，是不会执行下一个宏任务的。

### 9.将下面代码使用Promise改进
```js
setTimeout(function(){
    var a = 'hello'
    setTimeout(function(){
        var b = 'lagou'
        setTimeout(function(){
            var c = 'I ❤ U'
            console.log(a+b+c)
        }, 10)
    }, 10)
}, 10)
```
#### 答案: 
```js
const setTimeoutP = function (strArr) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(strArr)
        }, 10)
    })
}

// 同时执行
Promise.all([setTimeoutP('hello'), setTimeoutP('lagou'), setTimeoutP('I ❤ U')])
    .then(res => {
        const [a, b, c, ] = res
        console.log(a + b + c)
    })
// 链式执行
setTimeoutP(['hello'])
    .then(res=>{
        return setTimeoutP([...res,'lagou'])
    })
    .then(res=>{
        return setTimeoutP([...res,'I ❤ U'])
    })
    .then(res=>{
        const [a, b, c, ] = res
        console.log(a + b + c)
    })
```
### 10.简述下TypeScript与Javascript之间的关系
#### 答案: 
> TypeScript是JavaScript超集，编译过为JavaScript。TypeScript里提供了静态类型、类、模块、接口和类型注解，方便开发大型应用。

### 11.谈谈你所认为的TypeScript优缺点
#### 答案: 
> 优点增加了代码的可读性和可维护性,包容性较强,社区生态活跃。缺点是有一定的学习成本例如接口、泛型、类这些前端工程师可能不是很熟悉的概念。同时在开发项目是初期工作量会比较大，因为需要定义大量的Interfaces，但是在后续的维护和升级时又比JavaScript提高工作效率减少工作量。还有一个缺点是与某些库结合不是很完美,需要下载三方的声明文件

