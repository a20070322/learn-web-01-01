#### TypeScript
> 解决JavaScript自有类型系统的问题

* 强类型与弱类型(类型安全)
    >  （此处存在争议，以下观点仅代表个人理解） 编程语言分为强类型与弱类型，强类型有更强的类型约束，而弱类型中几乎没有什么约束
    * 强类型语言
        * 在语言层面就限制了函数的实参类型必须与形参类型相同
        * 不允许任意数据的隐式转换
        * 错误更早暴露 
        * 代码更智能，编码更准确
        * 重构更加牢靠
        * 减少不必要的类型判断
    * 弱类型语言
        * 在语言层面不会限制实参的类型
        * 语序任意数据的隐式转换
* 静态类型与动态类型(类型检查)
    * 静态类型语言
        * 一个变量声明时它的类型就是明确的
        * 声明后它的类型不允许在修改
        * 编译是需要做类型检查
    * 动态类型语言
        * 声明的变量是没有类型的
        * 变量中存放对的值是有类型的
* JavaScript自有类型系统特征
    * 弱类型且动态类型
    * 灵活多变
    * 可靠性低
    * 没有编译环节
```js
// 程序类型异常，必须等待执行时才会报错
const obj = {}
obj.foo()

// 两个数组相加,可能会传入非Number参数
funciton sum (a,d){
    return a + b
}
sum(100,'abc')

//可以使用任意类型作为属性 属性名会自动通过toString转换 
const obj = {}
obj[true] = 100
console.log(obj['true']) // 100
```
* Flow静态类型检查器
    > 2015年由FaceBook提供的一款工具，为JavaScript提供完善的类型检查方案
* 工作原理
    > 在代码中通过添加类型注解的方式来标记每一个变量或者参数的类型，flow 根据注解解析类型，从而发现类型错误。
* 安装
    ```sh
        # 安装
        yarn add flow-bin --dev
        # 初始化
        yarn flow init 
    ``` 
* 新建一个js文件且在最上部通过注释形式写 //@flow
    ```js
        // @flow
        function sum (a:number,b:number){
            return a + b
        }
        sum(1,2)
        sum('a','b')
    ```
* 执行  yarn flow
![](http://oss.ahh5.com/ahh5/md/202020200516144818.png)

* flow编译移除注解--flow-remove-types
```sh
    # 安装flow-remove-types 
    yarn add flow-remove-types --dev
    # 执行 flow-remove-types
    # . 当前目录  
    # -d 代表 --out-dir 
    # dist 输出目录 
    yarn flow-remove-types . -d dist  
```
* 编译后的文件
![](http://oss.ahh5.com/ahh5/md/202020200516145813.png)

* flow编译移除注解--babel
```sh
    # 安装 @babel/core babel核心模块
    yarn add @babel/core --dev
    # 安装 @babel/cli  babel命令行工具
    yarn add @babel/cli --dev
    # 安装 @babel/preset-flow  babel Flow注解插件
    yarn add @babel/preset-flow --dev
```
* 新建 .babelrc
```json
{
    "presets" : [
        "@babel/preset-flow"
    ]
}
```
```sh
    # 执行
    # src 工作目录
    # -d --out-dir  输出
    # dist 输出文件夹
    yarn babel src -d dist
```
![](http://oss.ahh5.com/ahh5/md/202020200516150816.png)

* flow + vscode+Flow Language Support 食用更加

* flow 类型注解
```js
@flow
// 形参类型注解
function sum (a:number,b:number){
    return a + b
}
// 变量类型注解
const num:number = 100
// 返回值类型注解
function foo():number{
    return 100
}
// 返回值为空的类型注解
function baz():void{
    return 100
}
```
* flow 原始数据类型注解
```js
const a : string = 'string'
const b : number = Infinity // NaN  Infinity 100 
const c : boolean = true // true false 
const d : null = null 
const e : void = undefined
const f : symbol = Symbol()

// 数组数据类型限制
const arr1: Array < string > = ['a', 'b', 'c']
const arr2: number[] = [1, 2, 3, 4, ]
// 固定长度的数组也可以成为元组数据类型
const arr3: [String, number] = ['a', 100]

// 对象数据类型限制
const obj: {
    foo: string,
    bar: number
} = {
    foo: 'string',
    bar: 100
}
// 可选成员
const obj2: {
    foo: string,
    bar ? : number
} = {
    foo: 'string',
    bar: 100
}
// 对象允许添加任意类型  但是 键只可以为 string 值只能是 number
const obj3: {
    [string]: [number]
} = {}
obj3.boo = 100
obj3.baz = 200

// 函数数据类型限制
function foo({
    num: number
}): number {
    return 100
}
// 设置回调函数据类型限制
function foo(callback: (number) => number): number {
    return callback()
}

// 特殊数据类型
// 字面量类型
const a: 'foo' = 'foo' //只能存放 foo字符串
// 联合变量
const type: 'success' | 'warning' | 'danger' = 'success' //只能存放 'success'|'warning'|'danger'

const b: string | number = 'string' //100

// 类型别名 or 用 type 声明一个类型
const StringOrNumber = string | number
const c : StringOrNumber = 'string' //100 

// mybe类型 
const gender : ?number = null

// Mixed 数据类型 强类型 所有类型的集合
function passMixed(value:Mixed){
    if(typeof value === 'string'){
        value.substr(1)
    }
    if(typeof value === 'number'){
        value * value
    }
}
passMixed(100)
passMixed('string')

// any数据类型 弱类型

function passAny(value:Any){
    value.substr(1)
    value * value
}
passAny(100)
passAny('string')

// 运行环境内置限制
const element: HTMLElement | null = document.getElementById('abc')
```

* TypeScript语言规范与基本应用
> TypeScript是JavaScript超集，编译过为JavaScript