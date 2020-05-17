# 异步编程
* 同步模式与异步模式
* 事件循环与消息队列
* 异步编程的几种方式
* Promise异步方案、宏方案/微任务队列
* Generator异步方案、Async/awit 语法糖
---
* 常见名词
    * CallStatck 调用栈
    * EventLoop  事件循环
    * WebApis    应用程序编程接口
    * Queue      消息队列
* 同步模式执行顺序和编译方式
    ```js
        function log(){
            console.log('log')
        }
        log()
        console.log('end')
    ```
    1. 首先函数外部会有类似匿名函数包裹形成可执行函数
        ```js
            //伪代码
            (function(){
                function log(){
                    console.log('log')
                }
                log()
            })()
        ```
    2. 将可执行函数放入调用栈中
    3. log()方法压入调用栈
    4. console.log('log')压入调用栈
    5. console.log('log')执行调用栈==>调用WebApi console.log方法 ===>输出到控制台
    6. console.log('log')弹出调用栈
    7. log() 弹出调用栈
    8. console.log('end')压入调用栈
    9. console.log('end')执行调用栈==>调用WebApi console.log方法===>输出到控制台
    10. console.log('end')弹出调用栈
    11. 程序执行结束

* 异步模式执行顺序和编译方式
    ```js
        function log(){
            setTimeout(function(){
                console.log('log')
            },1000)
        }
        log()
        console.log('end')
    ```
![](http://oss.ahh5.com/ahh5/md/202020200516223632.png)
1. 首先函数外部会有类似匿名函数包裹形成可执行函数
    ```js
        //伪代码
        (function(){
            function log(){
                let timer1 = setTimeout(function(){
                    console.log('log')
                },1000)
            }
            log()
        })()
    ```
2. 将可执行函数放入调用栈中
3. log()方法压入调用栈
4. setTimeout调用WebApi，函数投递至定时器任务设定 1000ms调用
5. log()方法弹出入调用栈
6. console.log('end')压入调用栈
7. console.log('end')执行调用栈==>调用WebApi console.log方法===>输出到控制台
8. console.log('end')弹出调用栈
9. timer1 计时器停止
10. 传入函数投递至 Queue
11. EventLoop 监听到 Queue 队列任务加入
12 传入函数投递至 调用栈
13. console.log('log')压入调用栈
14. console.log('log')执行调用栈==>调用WebApi console.log方法 ===>输出到控制台
15. console.log('log')弹出调用栈
16. 程序执行结束

### 回调函数（所有异步编程方案的根基）
> 有调用者定义，交给执行者的函数。将函数作为参数传递
* 缺点
    1. 不利于阅读
    2. 执行顺序混乱
    3. 回调地狱
* 变体
    1. 发布订阅
    2. 事件机制
```js
function foo(callback){
    setTimeout(function(){
        callback()
    },1000)
}
foo(function(){
    console.log('异步回调')
})
```
### Promise
> CommonJs 为异步编程提供统一、更合理、更强大的解决方案

```
graph LR
Promise-->承诺
承诺-->失败
承诺-->成功
失败-->onFulfilled
成功-->onRejected
```
> Promise里面就算是同步的也是放在队列中执行回调，所以end 会先打印
```js
const promise = new Promise((resolve,reject)=>{
    resolve(100) //承诺达成
    // reject(nesw Error('promise rejected')) //承诺失败
})
promise.then(val=>{
    console.log(val)
 },error=>{
    console.log(error)
})
console.log('end')
```
#### Promise使用案例--封装ajaxa
> 使用了webpack-dev-server 执行
```js
function ajax(url){
    return new Promise((resolve,reject)=>{
        var xhr = new XMLHttpRequest()
        xhr.open('GET',url)
        xhr.responseType = 'json'
        xhr.onload=function(){
            if(this.status === 200){
                resolve(this.response)
            }else{
                reject(new Error(this.statusText))
            }
        }
        xhr.send()
    })
}
ajax('/api/test.json')
.then(res=>{
    console.log(res)
},err=>{
    console.log(err)
})
ajax('/api/test2.json')
.then(res=>{
    console.log(res)
},err=>{
    console.log(err)
})
```
* 成功
![](http://oss.ahh5.com/ahh5/md/202020200515232703.png)
* 失败
![](http://oss.ahh5.com/ahh5/md/202020200515232836.png)

#### Promise常见误区
> 如果在then方法里面继续调用Promise就会陷入回调地狱，这样是错误的
```js
ajax('/api/test.json')
    .then(res => {
        ajax('/api/list.json')
            .then(res => {
                ajax('/api/list2.json')
                    .then(res => {

                    })
            })
    })
```
#### Promise链式调用
* Promise 对象的then方法会返回一个全新的Promise对象
* 后面的then方法就是在为上一个then返回的Promise注册回调
* 前面的then方法中回调函数的返回值会作为后面then方法的回调参数
* 如果回调中返回的是Promise，那后面的then方法的回调就会等待它的结束

```js
ajax('/api/test.json')
    .then(res => {
        console.log(res)
        return ajax('/api/list.json')
    })
    .then(res => {
        console.log(res)
        return ajax('/api/list2.json')
    })
    .then(res => {
        console.log(res)
    })
```
![](http://oss.ahh5.com/ahh5/md/202020200515234017.png)

![](http://oss.ahh5.com/ahh5/md/202020200515234048.png)

#### Promise异常处理
```js
ajax('/api/test.json')
    .then(res => {
        console.log(res)
        return ajax('/api/list.json')
    })
    .then(res => {
        console.log(res)
        return ajax('/api/list2.json')
    })
    .then(res => {
        console.log(res)
    })
    .catch(err=>{
        cosno.log(err)
    })
```
![](http://oss.ahh5.com/ahh5/md/202020200515234705.png)

#### Promise静态方法
* Promise.resolve 成功  返回一个Promise对象
```js
Promise.resolve('foo')
.then(res=>{
    console.log(res)//foo
})
```
* Promise.reject 失败  返回一个Promise对象
```js
Promise.reject(new Error('err'))
.catch(err=>{
    console.log(err)
})
```
#### Promise并行
* Promise.all 等待所有任务结束才会执行then 并且都成功则成功，否则失败
```js
const p1 = ajax('/api/test.json')
const p2 = ajax('/api/list.json')
const p3 = ajax('/api/list2.json')
// const p4 = ajax('/api/err.json')
Promise.all([p1,p2,p3])
.then(res=>{
    console.log(res)
})
.catch(err=>{
    console.log(err)
})
```
![](http://oss.ahh5.com/ahh5/md/202020200516001059.png)
* Promise.race 有一个任务执行完毕就会触发
> 实现ajax请求超时控制方式
```js
const p1 = ajax('/api/test.json')
const timer = new Promise((resolve,resolve)=>{
    setTimeout(()=>reject(new Error('timeout')),500)
})
Promise.race([p1,timer])
.then(res=>{
    console.log(res)
})
.catch(err=>{
    console.log(err)
})
```
#### Promise执行时序/宏任务 vs 微任务
* 回调队列中的任务有一个中说法叫"宏任务"
* 直接在本文执行的末尾执行"微任务"
* Promise作为微任务
* 大部分的异步调用api都是宏任务的体现
* Promise&&MutationObserver 是微任务
```js
console.log('start')
setTimeout(()=>console.log('setTimeout'),0)
Promise.resolve()
.then(res=>{
    console.log('Promise')
})
.then(res=>{
    console.log('Promise')
})
.then(res=>{
    console.log('Promise')
})
console.log('end')
```
![](http://oss.ahh5.com/ahh5/md/202020200516002906.png)


### Generator异步方案
```js
// 定义主函数
function * main (){
    const data = yield ajax('/api/test.json')
    const list = yield ajax('/api/list.json')
    const list2 = yield ajax('/api/list2.json')
    console.log(data,list,list2)
    // {data: Array(2)} {data: "list"} {data: "list"}
}
const g = main()
const result = g.next()
result.value.then(res=>{
    const res2 =  g.next(res)
    res2.value.then(res=>{
        const res3 =  g.next(res)
        g.next(res)
    })
})
```
> 优化上面代码
```js
function* main() {
    try {
        // 同步调用
        const data = yield ajax('/api/test.json')
        const list = yield ajax('/api/list.json')
        const list2 = yield ajax('/api/list2.json')
        console.log(data, list, list2)
        // {data: Array(2)} {data: "list"} {data: "list"}
    } catch (error) {
        // 捕获异常处理
        console.log(error)
    }

}
const g = main()
// 生成器函数执行器
function handleResult(result) {
    if (result.done) return //生成器函数结束
    result.value.then(data => {
        // 进行到下一个生成器
        handleResult(g.next(data))
    },err=>{
        // 捕获异常 传递
        g.throw(err)
    })
}
handleResult(g.next())
```
> 封装co 生成器函数执行器
```js
function* main() {
    try {
        // 同步调用
        const data = yield ajax('/api/test.json')
        const list = yield ajax('/api/list.json')
        const list2 = yield ajax('/api/list2.json')
        console.log(data, list, list2)
        // {data: Array(2)} {data: "list"} {data: "list"}
    } catch (error) {
        // 捕获异常处理
        console.log(error)
    }

}
function co(generator) {
    const g = generator()
    // 生成器函数执行器
    function handleResult(result) {
        if (result.done) return //生成器函数结束
        result.value.then(data => {
            // 进行到下一个生成器
            handleResult(g.next(data))
        }, err => {
            // 捕获异常 传递
            g.throw(err)
        })
    }
    handleResult(g.next())
}
co(main)
```

### Async/Await语法糖 ECMA2017
```js
async function main() {
    try {
        // 同步调用
        const data = await ajax('/api/test.json')
        const list = await ajax('/api/list.json')
        const list2 = await ajax('/api/list7.json')
        console.log(data, list, list2)
        // {data: Array(2)} {data: "list"} {data: "list"}
        return [data, list, list2] //返回值可以直接传到 then 方法
    } catch (error) {
        // 捕获异常处理
        console.log(error)
        throw error // 继续触发 错误可以在外部的catch 捕获
    }

}
const promise = main()
promise.then(res=>{
    console.log(res) // (3) [{…}, {…}, {…}]
})
.catch(err=>{
    console.log(err)
})
```
