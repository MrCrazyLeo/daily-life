# daily-life

# 2021-01-06

## ES6
[1.5万字概括ES6全部特性(已更新ES2020](https://juejin.cn/post/6844903959283367950#heading-39)

![170de4be1318de5e](img/170de4be1318de5e.png)

### ES2015新特性

let、const、dead zone静态死区

箭头函数

for...of

Promise

Generator

Class

Module

Set()

Map()

Proxy

Reflect

Symbol

### ES2016新特性



### ES8新特性

async/await

> [ES2017 标准引入了 async 函数，使得异步操作变得更加方便](https://es6.ruanyifeng.com/#docs/async)



# 2021-01-05

### JS大数问题

#### 最大整数问题

因为JS的Number类型是遵循`IEEE 754`规范表示，这就意味着JavaScript能精确表示的数字是优先的，可以精确到个位的最大整数是9007 1992 5474 0992（9千万亿多），也就是2^53，超过这个范围精度就会丢失，所以出现下边现象：

```javascript
Math.pow(2, 53);    // 9007199254740992
Math.pow(2, 53) === Math.pow(2, 53) + 1;    // true
9007199254740992 === 9007199254740992 + 1;    // true
```

**解决方案**:

#### 最大浮点数问题

前面讲到，在`JavaScript`中，使用浮点数标准`IEEE 754`表示数字的，在表示小数的时候，在转化二进制的时候有些数是不能完整转化的，比如0.3，转化成二进制是一个很长的循环的数，是超过了`JavaScript`能表示的范围的，所以近似等于0.30000000000000004。这个是二进制浮点数最大的问题（不仅 JavaScript，所有遵循 IEEE 754 规范的语言都是如此）。所以要判断两个值是否相等，可以用ES6引入的极小常量`Number.EPSILON`。Number.EPSILON是JS实际能达到的最小精度，比2^(-52)大

```javascript
function IsEqual(num1,num2){
  let EPSILON = Number.EPSILON ? Number.EPSILON : Math.pow(2,-52)
  return Math.abs(num1 - num2) < EPSILON
}
```



## BigInt

为解决大数（大于2^53-1）问题，JS内置了一种新的数字基本类型BigInt，可以表示任意精度**整数**。（注意，是`整数`）

> 可以用在一个整数字面量后面加 `n` 的方式定义一个 `BigInt` ，如：`10n`，或者调用函数`BigInt()`

它在某些方面类似于 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) ，但是也有几个关键的不同点：不能用于 [`Math`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math) 对象中的方法；不能和任何 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 实例混合运算，两者必须转换成同一种类型。在两种类型来回转换时要小心，因为 `BigInt` 变量在转换成 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 变量时可能会丢失精度。

以下操作符可以和 `BigInt` 一起使用： `+`、``*``、``-``、``**``、``%`` 。除 `>>>` （无符号右移）之外的 [位操作](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators) 也可以支持。因为 `BigInt` 都是有符号的， `>>>` （无符号右移）不能用于 `BigInt`。[为了兼容 asm.js ](https://github.com/tc39/proposal-bigint/blob/master/ADVANCED.md#dont-break-asmjs)，`BigInt` 不支持单目 (`+`) 运算符。

`/` 操作符对于整数的运算也没问题。可是因为这些变量是 `BigInt` 而不是 `BigDecimal` ，该操作符结果会向零取整，也就是说不会返回小数部分。

```javascript
const rounded = 5n / 2n;
// ↪ 2n, not 2.5n
```

`BigInt` 和 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 不是严格相等的，但是宽松相等的。[`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 和 `BigInt` 可以进行比较。

注意被 `Object` 包装的 `BigInt`s 使用 object 的比较规则进行比较，只用同一个对象在比较时才会相等。

```javascript
0n === Object(0n); // false
Object(0n) === Object(0n); // false

const o = Object(0n);
o === o // true
```

对任何 `BigInt` 值使用 [`JSON.stringify()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 都会引发 `TypeError`，因为默认情况下 `BigInt` 值不会在 `JSON` 中序列化。但是，如果需要，可以实现 `toJSON` 方法：

```javascript
JSON.stringify(BigInt(1)); // Uncaught TypeError: Do not know how to serialize a BigInt
// 重新实现toJSON方法
BigInt.prototype.toJSON = function() { return this.toString(); }
JSON.stringify(BigInt(1));
// '"1"'
BigInt(10).toString // '10' 会转成数值再转成字符串，所以依旧有精度损失问题
```



## instanceof原理

实际就是考察原型链的问题
> **instanceof 检测一个对象A是不是另一个对象B的实例的原理：**
>
> 查看对象B的prototype指向的对象是否在对象A的__proto__链上。如果在，则返回true,如果不在则返回false。不过有一个特殊的情况，当对象B的prototype为null将会报错(类似于空指针异常)。
>
> ```javascript
> function _instanceof(A, B) {
>     var O = B.prototype;// 取B的显示原型
>     A = A.__proto__;// 取A的隐式原型
>     while (true) {
>         //Object.prototype.__proto__ === null
>         if (A === null)
>             return false;
>         if (O === A)// 这里重点：当 O 严格等于 A 时，返回 true
>             return true;
>         A = A.__proto__;
>     }
> }
> 
> var Person = function() {};
> var student = new Person();
> console.log(student instanceof Person);  // true
> _instanceof(student,Person) // true
> ```



# 2021-01-04

## no-cache 和 no-store的区别

- no-cache和no-store都是HTTP协议头Cache-Control的值

  - no-store 彻底禁用缓存，所有内容都不会被缓存（无论是本地还是服务器），每次都从服务器获取
  - no-cache 可以本地缓存，可以代理服务器缓存，在浏览器使用缓存前，会往返对比Etag，如果Etag没变，返回304，则使用缓存

- 除了no-cache和no-store，Cache-Control头的取值还有：

  - public 所有内容都被缓存（客户端、代理服务器都缓存）

  - private 客户端缓存、代理服务器不缓存

  - max-age **常见**

    > 缓存的内容将在 xxx 秒后失效，这个选项只在 HTTP1.1 可用，并如果和 Last-Modified 一起使用时，优先级较高。



## webpack loader加载顺序

loader加载顺序从右往左
```javascript
require("!style!css!less!bootstrap/less/bootstrap.less");

//=> the file "bootstrap.less" in the folder "less" in the "bootstrap"

//module (that is installed from github to "node_modules") is

//transformed by the "less-loader". The result is transformed by the

//"css-loader" and then by the "style-loader".

//If configuration has some transforms bound to the file, they will not be applied.
```



## Vue响应式原理

### 十二字真言

数据劫持 收集依赖 派发更新



## JS精度问题

https://www.html.cn/archives/7340

> 加法：
>
> 0.1 + 0.2 = 0.30000000000000004
>
> 减法：
>
> 0.3 - 0.2 = 0.09999999999999998
>
> 乘法：
>
> 19.9 * 100 = 1989.9999999999998
>
> 9.96 * 10 = 99.60000000000001
>
> 除法：
>
> 0.3 / 0.2 = 1.4999999999999998

处理大数也会有问题！



## 一道面试题：对于CORS，GET、POST的区别

GET是简单请求，不会触发Option

POST在请求头传入格式为formdata时是简单请求，传入json的话是复杂请求，会进行option验证



## for of 、for in 区别  

1. for in循环的是key，for of循环的是value

2. for of是ES6引入的特性，修复了ES5引入的for in的不足

   ```javascript
   let aArray = ['a',123,{a:'1',b:'2'}]
   for(let index in aArray){
       console.log(`${aArray[index]}`);
   } // a 123 [object Object]
   
   // 此时给aArray增加一个name的属性
   aArray.name ='xxx'
   for(let index in aArray){
       console.log(`${aArray[index]}`);
   } // a 123 [object Object]
   
   // 此时aArray已经是object了...
   // 如果实在想用for...of来遍历普通对象的属性的话，可以通过和Object.keys()搭配使用，先获取对象的所有key的数组,然后遍历
   for(var key of Object.keys(aArray)){
       //使用Object.keys()方法获取对象key的数组
       console.log(key+": "+aArray[key]);
   } 
   // 0: a
   // 1: 123
   // 2: [object Object]
   
   ```

3. `for...of`不能循环普通的对象，需要通过和`Object.keys()`搭配使用 。for of更适合遍历数组，for in适合遍历对象。for in也可以遍历数组，但是会有如下问题：

   1. index索引为字符串型数字，不能直接进行几何运算；
   2. 遍历顺序有可能不是按照实际数组的内部顺序；
   3. 使用for in会遍历数组所有的可枚举属性，包括原型。
   
   

## 设计模式在前端的应用

###  迭代器模式

```javascript
// async/await就是借助Promise与Generator生成器

```



### Echarts与D3的区别

1. 兼容性：Echarts兼容到ie6以上的主流浏览器，D3兼容到i9以上的主流浏览器

2. 依赖：D3依赖于一个个Svg标签，直接操作DOM，所以比较耗费性能；Echarts依赖canvas，则没有这个问题

3. 学习成本：D3比较底层，API众多，学习成本比较高；Echarts使用Options API，上手比较容易

4. 灵活性：D3偏函数式编程，可以做非常丰富的定制；echarts封装成一个一个Option配置项，灵活性较差

（共性：都是数据可视化工具，都免费开源）



## 浏览器缓存

### 强缓存：

1. **Expires** 资源过期日期

   > Expires: Wed, 11 May 2018 07:20:00 GMT

2. **Cache-Control**

   >public, max-age=31536000 

### 协商缓存：

1. **Last-Modified、If-Modified-Since** 

   > `Last-Modified` 表示本地文件最后修改日期，浏览器会在request header加上`If-Modified-Since`（上次返回的`Last-Modified`的值），询问服务器在该日期后资源是否有更新，有更新的话就会将新的资源发送回来
   >
   > 但是如果在本地打开缓存文件，就会造成 Last-Modified 被修改，所以在 HTTP / 1.1 出现了 ETag
   >
   > `Last-Modified`仅支持秒级更新，所以当文件频繁更换时导致结果不准确，也需要Etag

2. **ETag、If-None-Match**

   > `ETag`的优先级比`Last-Modified`更高
   >
   > 使用`ETag`的原因：
   >
   > - 一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET；
   > - 某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)；
   > - 某些服务器不能精确的得到文件的最后修改时间。

第一次请求资源：

![606026927-59315bafce189_articlex](img/606026927-59315bafce189_articlex.png)

之后请求资源：

![3768101591-59312984bf500_articlex](img/3768101591-59312984bf500_articlex.png)





# 2021-01-03

### 用ES5实现私有变量

核心：基于闭包
```javascript
function Person(name){
  const _name = name
  this.getName = function(){
    return _name
  }
}
const p = new Person('xxx')
console.log(p._name) // undefinded
console.log(p.getName()) // xxx
```







# 2021-01-02

## 一道算法题： 乱序排序（洗牌。）

```javascript
const arr = [1,2,3,4,5,6,7]
// Fisher-Yates洗牌算法，实现十分简单，并且它可以保证均匀性，即元素的各种排列顺序出现的概率都相等
function shuffle(arr){
  for(let i=arr.length - 1;i>0;i--){
    const j = Math.floor(Math.random() * (i+1))
    const temp = arr[i]
    arr[i] = arr[j]
    arr[j] = temp
  }
}
shuffle(arr)
console.log(arr)
```

顺便提一嘴，一开始我想的是下边这种解决方式，这种洗牌方式出来的结果是不等概率

```javascript
const arr = [1,2,3,4,5,6,7]
function shuffle(arr){
  const len = arr.length
  for(let i=0;i<;i++){
  	const j = Math.floor(Math.random() * len)
    const temp = arr[i]
    arr[i] = arr[j]
    arr[j] = temp
  }
}
shuffle(arr)
console.log(arr)
```

> 其原理是，在第 i 次循环中，从**所有元素**中等可能地选一个元素，与第 i 个元素交换。这种算法的错误可以如下证明：对于一个长度为 ![[公式]](https://www.zhihu.com/equation?tex=+n) 的数组，算法创造了 ![[公式]](https://www.zhihu.com/equation?tex=n%5En) 个等可能的基本事件，这些事件对应于 ![[公式]](https://www.zhihu.com/equation?tex=n%21) 种排列顺序。在非平凡情况下， ![[公式]](https://www.zhihu.com/equation?tex=n%5En) 不能被 ![[公式]](https://www.zhihu.com/equation?tex=n%21) 整除，所以各种排列顺序不可能等概率。
>
> 出自：[10809 一种错误的洗牌算法，以及乱排常数 (1)](https://zhuanlan.zhihu.com/p/31547382)



## 一道面试题： 水平垂直居中

### 行内元素水平居中

```css
text-align:center;
```

### 块状元素水平居中

```css
margin: 0 auto
```

###   flex 

```css
{
  display: flex;
  justify-content: center; /*使子项目水平居中*/
  align-items: center; /*使子项目垂直居中*/
}
```

### 已知高度宽度元素的水平垂直居中

#### 绝对定位与负边距实现 

```css
#container{
    position:relative;
}
 
#center{
    width:100px;
    height:100px;
    position:absolute;
    top:50%;
    left:50%;
    margin:-50px 0 0 -50px;
}
```

![绝对定位与负边距](img/绝对定位与负边距.jpg)

#### 绝对定位与margin

```css
#container{
    position:relative;
}
 
#center{
    position:absolute;
    margin:auto;
    top:0;
    bottom:0;
    left:0;
    right:0;
}
```

### 未知高度和宽度元素的水平垂直居中

#### 当要被居中的元素是inline或者inline-block元素

```css
#container{
    display:table-cell;
    text-align:center;
    vertical-align:middle;
}
#center{}
```

#### Css3的transform

```css
#container{
    position:relative;
}
 
#center{
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```



## 一道面试题：去重

**常规方法**

```javascript
var arr=[1,2,3,3,4,5,5,6,6]
var res = []
for(let i=0;i<arr.length;i++){
 if(res.indexOf(arr[i]) === -1) {
   res.push(arr[i])
 }
}
console.log('去重之后的数组 ',res)
```

**ES6**

```javascript
const arr=[1,2,3,3,4,5,5,6,6]
const t = new Set(arr)
[...t]

// 或
const arr=[1,2,3,3,4,5,5,6,6]
Array.from(new Set(arr))

// 或使用正则表达式
// (arr+',').replace(/(\d+,)\1+/ig,'$1') -> "1,2,3,4,5,6,"
// (arr+',').replace(/(\d+,)\1+/ig,'$1').split(',') -> ["1", "2", "3", "4", "5", "6", ""]
(arr+',').replace(/(\d+,)\1+/ig,'$1').split(',').slice(0,-1) // ["1", "2", "3", "4", "5", "6"]
```



# 2021-01-01

## 前端安全常见问题

### CSRF 跨站请求伪造

针对客户，利用客户身份干坏事。CSRF是攻击者借助受害者cookie骗取服务器新人，可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受供给服务器，从而在并未授权的情况下执行权限保护之内的操作



### XSS 跨站脚本攻击

XSS是恶意攻击者往网页嵌入恶意脚本代码，当用户浏览网页的时候，脚本执行，达到恶意攻击用户的目的



# 2020-12-31

## babel实现的原理

1. 解析

   通过解析器babylon将代码解析成抽象语法树AST

2. 转换

   通过**babel-traverse plugin**对抽象树进行深度优先遍历，遇到需要转换的，就直接在AST对象上对节点进行添加、更新及移除操作，比如遇到箭头函数，就转换成普通函数，最后得到新的AST树

3. 生成

   通过**babel-generator**将AST树生成es5代码



# 2020-12-30

## 从URL到页面渲染经历了什么？

### 1.DNS查询

### 2.TCP连接

### 3.HTTP请求

### 4.服务器响应

### 5.客户端渲染



## \<dns-prefetch\> \<preconnect\>

```html
<link ref="dns-prefetch">
```

这个是启用现代浏览器的dns预解析。对于要重定向的域名有效。

> 最优的方案应该是：通过js初始化一个iframe异步加载一个页面，而这个页面里包含本站所有的需要手动dns prefetching的域名。

\<preconnect\>预连接

> 尽管 `dns-prefetch` 仅执行 DNS查找，但`preconnect` 会建立与服务器的连接。如果站点是通过HTTPS服务的，则此过程包括DNS解析，建立TCP连接以及执行TLS握手。将两者结合起来可提供进一步减少[跨域请求](https://wiki.developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS)的感知延迟的机会。您可以安全地将它们一起使用,如下所示：
>
> ```html
> <link rel="preconnect" href="https://fonts.gstatic.com/" crossorigin>
> <link rel="dns-prefetch" href="https://fonts.gstatic.com/">
> ```



##  缓存机制和对应HTTP字段



## 一道链式调用的面试题（来自三七互娱）：Hero("37er")

> 编写代码，满足以下条件：  
>
>   （1）Hero("37er");执行结果为 
>
>   Hi! This is 37er 
>
>   （2）Hero("37er").kill(1).recover(30);执行结果为 
>
>   Hi! This is 37er 
>
>   Kill 1 bug 
>
>   Recover 30 bloods 
>
>   （3）Hero("37er").sleep(10).kill(2)执行结果为 
>
>   Hi! This is 37er 
>
>   //等待10s后 
>
>   Kill 2 bugs //注意为bugs 
>
>   （双斜线后的为提示信息，不需要打印）
>
> ```javascript
> // 使用构造函数的形式
> function Hero(name) {
>   this.name = name;
>   console.log(name);
>   return this;
> }
> 
> Hero.prototype.kill = function () {
>   console.log('Kill');
>   return this
> }
> 
> Hero.prototype.sleep = function (time) {
>   var start = new Date().getTime();
>   console.log('Timeout');
>   while((start + time * 1000) > new Date().getTime()) {
> 
>   }
>   return this;
> }
> 
> // 创建一个实例
> var hero = function(name) { 
>   return new Hero(name);
> }
> hero("37er").sleep(10).kill(2)
> ```
>
> ```javascript
> // 使用对象的形式
> var Hero = function(text) {
>     console.log('Hi! This is ' + text)
>     var obj = {
>         kill: function(num) {
>         if (num == 1) {
>           console.log(`Kills ${num} bug`);
>         } else {
>           console.log(`Kills ${num} bugs`);
>         }
>                 return this;
>     },
>     sleep: function(time) {
>       var start = new Date().getTime();
>             while((start + time*1000) > new Date().getTime()) {
>         
>             }
>       			console.log('')
>             return this;
>     },
>     recover: function(num) {
>             console.log('Recover ' + num + 'bloods');
>             return this;
>         }
>     }
>   return obj;
> }
> ```
>
> 



# 2020-12-29

##  HTTP请求头与响应头

### 浏览器请求头
- Accept(text/html、\*/\*)
- Accept-Encoding
- Accept-Language
- Connection(keep-alive、close)
- Host(发送请求时，该报头域是必需的)
- Referer
- User-Agent
- Cache-Control(默认为private)
  - **Cache-Control:private** 默认为private 响应只能够作为私有的缓存，不能在用户间共享
  - **Cache-Control:public**响应会被缓存，并且在多用户间共享。正常情况, 如果要求HTTP认证,响应会自动设置为 private.
  - **Cache-Control:must-revalidate** 响应在特定条件下会被重用，以满足接下来的请求，但是它必须到服务器端去验证它是不是仍然是最新的。
  - **Cache-Control:no-cache** 响应不会被缓存,而是实时向服务器端请求资源。
  - **Cache-Control:max-age=10** 设置缓存最大的有效时间，但是这个参数定义的是时间大小（比如：60）而不是确定的时间点。单位是[秒 seconds]。
  - **Cache-Control:no-store **在任何条件下，响应都不会被缓存，并且不会被写入到客户端的磁盘里，这也是基于安全考虑的某些敏感的响应才会使用这个。
- Range 断点续传
  **Range:bytes=0-5** 指定第一个字节的位置和最后一个字节的位置。用于告诉服务器自己想取对象的哪部分。
### 服务端响应头

- Cache-Control

- Content-Type

  **Content-Type：text/html;charset=UTF-8** 告诉客户端，资源文件的类型，还有字符编码，客户端通过utf-8对资源进行解码，然后对资源进行html解析。通常我们会看到有些网站是乱码的，往往就是服务器端没有返回正确的编码。

- Content-Encoding

  **Content-Encoding:gzip** 告诉客户端，服务端发送的资源是采用gzip编码的，客户端看到这个信息后，应该采用gzip对资源进行解码

- Date 服务器时间

  **Date: Tue, 03 Apr 2018 03:52:28 GMT** 这个是服务端发送资源时的服务器时间，GMT是格林尼治所在地的标准时间。http协议中发送的时间都是GMT的，这主要是解决在互联网上，不同时区在相互请求资源的时候，时间混乱问题。

- Server 服务器和对应版本

- Transfer-Encoding

  **Transfer-Encoding：chunked** 这个响应头告诉客户端，服务器发送的资源的方式是分块发送的。一般分块发送的资源都是服务器动态生成的，在发送时还不知道发送资源的大小，所以采用分块发送，每一块都是独立的，独立的块都能标示自己的长度，最后一块是0长度的，当客户端读到这个0长度的块时，就可以确定资源已经传输完了。

- Expires 过期时间

- **Expires:**

  **Sun, 1 Jan 2000 01:00:00 GMT** 这个响应头也是跟缓存有关的，告诉客户端在这个时间前，可以直接访问缓存副本，很显然这个值会存在问题，因为客户端和服务器的时间不一定会都是相同的，如果时间不同就会导致问题。所以这个响应头是没有Cache-Control：max-age=*这个响应头准确的，因为max-age=date中的date是个相对时间，不仅更好理解，也更准确。

- Last-Modified 最后修改时间

  **Last-Modified: Dec, 26 Dec 2015 17:30:00 GMT** 所请求的对象的最后修改日期(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示)

- Connection

- Etag
  **ETag: "737060cd8c284d8af7ad3082f209582d"** 就是一个对象（比如URL）的标志值，就一个对象而言，比如一个html文件，如果被修改了，其Etag也会别修改，所以，ETag的作用跟Last-Modified的作用差不多，主要供WEB服务器判断一个对象是否改变了。比如前一次请求某个html文件时，获得了其 ETag，当这次又请求这个文件时，浏览器就会把先前获得ETag值发送给WEB服务器，然后WEB服务器会把这个ETag跟该文件的当前ETag进行对比，然后就知道这个文件有没有改变了。

- Refresh 刷新时间

- Access-Control-Allow-Origin 允许跨域的域名

- Access-Control-Allow-Methods 允许访问的方法

- Access-Control-Allow-Credentials

  **Access-Control-Allow-Credentials: true** 是否允许发送cookie。默认情况下，Cookie不包括在CORS请求之中。设为true，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值也只能设为true，如果服务器不要浏览器发送Cookie，删除该字段即可。如果access-control-allow-origin为*，当前字段就不能为true

- Content-Range

## 单例模式

### 普通单例模式：

 核心：调用创建单例函数式才占用内存

  ```js
  // 普通单例
  var Singleton = function(name) {
       this.name = name;
    	// 已经占用内存了...
       this.instance = null;
  };
  Singleton.getInstance = function(name) {
       if (!this.instance) {
           this.instance = new Singleton(name);
       }
       return this.instance;
  };
  var a = Singleton.getInstance('sven1');
  var b = Singleton.getInstance('sven2');
  alert(a === b); // true
  
  
  // 惰性单例
  var Singleton = function(name) {
       this.name = name;
  };
  Singleton.getInstance = (function() {
       // 调用时才占用内存空间
       var instance = null;
       return function(name) {
           if (!instance) {
               instance = new Singleton(name);
           }
           return instance;
       }
  })();
  var a = Singleton.getInstance( 'sven1' );
  var b = Singleton.getInstance( 'sven2' );
  alert ( a === b ); // true
  ```

### 透明的单例模式

  ```javascript
  // 所谓透明，就是可以直接new，而不用像上边一样记住Singleton.getInstance写法
  const CreateDiv = (function(){
   	let instance;
    const _CreateDiv = function(html){
      if(instance) {
        return instance
      }
      this.html=html
      this.init()
      return instance = this
    }
    _CreateDiv.prototype.init = function() {
      const div=document.createElement('div')
      div.innerHTML = this.html
      document.body.appendChild(div)
    }
    return _CreateDiv
  })()
  var a = new CreateDiv('sven1');
  var b = new CreateDiv('sven2');
  alert(a === b); // => true
  ```

### 代理实现单例模式

  ```javascript
  // 普通的创建 div 的类：
  const CreateDiv = function(html) {
       this.html = html;
       this.init();
  };
  CreateDiv.prototype.init = function() {
       var div = document.createElement('div');
       div.innerHTML = this.html;
       document.body.appendChild(div);
  };
  // 代理
  const ProxySingletonCreateDiv = (function() {
      let instance;
      return function(html) {
          if (!instance) {
              instance = new CreateDiv(html);
          }
          return instance;
      }
  })();
  const a = new ProxySingletonCreateDiv('sven1');
  const b = new ProxySingletonCreateDiv('sven2');
  alert(a === b);
  ```

### 思考题：

> 问题描述：如果想要实现一个高阶函数，高阶函数接受一个函数作为参数，返回一个新的函数，这个新的函数和原函数功能一样，但是多了单例的效果，如何实现？
>
> ```javascript
> const Singleton = function(fn){
>   const instance;
>   return function() {
>     return instance || instance = fn.apply(this, arguments)
>   }
> }
> ```
>
> 

# 2020-12-27 

## 设计模式的核心是方便代码的维护和项目重构？

![微信截图_20201227131555](img/微信截图_20201227131555.png)

## 一道面试题 —— 获取一张图片，若获取失败则报错

```javascript
  const imgAddress = "xxxxx"
  const imgPromise = (url) => {
    return new Promise((resolve,reject) => {
      const img = new Image();
      img.src = url
      img.onload =() => {
        resolve(img)
      }
      img.onerror = () => {
        reject(new Error('图片加载出错'))
      }
    })
  }
  imgPromise(imgAddress).then(img => {
    document.body.appendChild(img)
  }).catch(err => {
    document.body.innerHTML = err
  })
```

## 一道面试题 —— 柯里化函数

```javascript
// 时间监听，ie是只支持attchEvent
const whichEvent = (function() {
  if(window.addEventListener) {
    // element绑定事件的元素、type监听类型、listener执行的回调函数、useCapture是捕获还是冒泡
    return function(element, type, listener, useCapture) {
      element.addEventListener(type, function(e){
        listener.call(element,e)
      }, useCapture)
    }
  } else if(window.attachEvent){
    // ie只支持冒泡
    return function(element, type, handler) {
      element.attachEvent('on'+type, function(e){
        handler.call(element, e)
      })
    }
  }
})()
```



```javascript
// 实现 add(1)(2)(3) -> 6 ， add(1,2,3)(4) -> 10 ，add(1)(2,3,4) -> 10

```



# 2020-12-26 

## 前端是否需要写自动测试的代码

```javascript
if(你写的是一个utils类 || 你写的是一个公共component || 你写的是一个开源项目)
  return 你需要写单元测试（UT）代码
```

![WX20201226-231903@2x](img/WX20201226-231903@2x.png)

![WX20201226-232056@2x](img/WX20201226-232056@2x.png)

# 2020-12-25

## 代码评审（Code Review)的作用

- 降低低级bug产出，提高代码产出质量
- 学习别人更好的代码风格和解决方案
- 代码评审可以更好地评估工时，因为业务有时候是相似的，通过回顾以往代码，可以了解该模块的业务背景及开发手段，从而优化更好的方案，对于评估做类似模块的工时也会更为准确
- 代码评审让团队成员不再只是涉及自；己开发的模块，而是对别人模块也有所了解，这样其他成员可以在后续工期接手前人的代码
- 代码评审有助于指导培养新工程师

- 需求评审的作用
  - 明白需求的背景和目标
  - 统一需求实现的过程、方案以及相关功能点
  - 让参会成员清楚各种任务以及完成时间
  - 确认研发和测试公式，产品经理按照需求上线时间判断是否需要拆分任务、增加资源

# 2020-12-16

## Typescript的优势到底在哪？

- 类型推断，减少“不知是字符串还是数字所以要转成数字”类似的兜底情况
- 一边写代码一边产出半成品文档（因为注释较多） —— 这是个“成也萧何败也萧何”的问题
- 杜绝了手误导致的变量名错误（找不到该变量命就会报错提示）
- **静态类型有利于构建大型应用**，静态类型检查可以做到early fail，即你编写的代码即使没有被执行到，一旦你编写代码时发生类型不匹配，语言在编译阶段（解释执行也一样，可以在运行前）即可发现。针对大型应用，测试调试分支覆盖困难，很多代码并不一定能够在所有条件下执行到。而假如你的代码简单到任何改动都可以从UI体现出来，这确实跟大型应用搭不上关系，那么静态类型检查确实没什么作用。

