# ✨HTML

## viewport

```html
<meta viewport="width=device-width, initial-scale=1.0">
```

viewport的字段有



# ✨CSS
## 布局
### flex

经典的题目就是用flex布局画一颗🎲啦。![dice_flex](img/dice_flex.png)

### grid

也可以拿🎲来练手





### 响应式布局



## CSS3专题


# ✨JavaScript

# ✨网络通信

## Cookie

Cookie字段包括max-age（曾经是expires）、domain、path、secure、http-only

Cookie与本地存储的区别：

	- Cookie在旧版浏览器上单独域名下可能限制最多20个，新的浏览器则没限制数量；单个Cookie大小一般不超过4kB，因为是携带在请求头，如果体积过大，会对传输效率产生影响；Cookie得自己封装setCookie、getCookie、deleteCookie等方法；Cookie可以跨域
	- 本地存储包括localStorage和sessionStorage（IndexDB这里不谈）。它们俩没有大小限制，只能存储在本地，有getItem、setItem、clear等浏览器自带的方法



# ✨算法


## 树

层次遍历

```javascript
var tree = {
  val:1,
  left: {
      val: 2,
      left: null,
      right: null
  },
  right: {
      val:3,
      left: {
          val: 31
      },
      right:{
          val: 32
      }   
  }
}
// 迭代！ 时间复杂度O(n)，空间复杂度O(n)
var levelOrder = function(root) {
    let result = []
    if(!root) return result
  	// const res = [].push(root) 与 const res = [root] 与 下边的写法有什么区别？
    const res = []
    res.push(root)
    let level = 0
    while(res.length !== 0){
        result[level] = []
        const len = res.length
        // 注意，len要提出来，不能在下边循环体用res.length，你就在面试中犯了这个错误！太不应该了
        for(let i=0;i<len;i++){
            const item = res.shift()
            result[level].push(item.val)
            item.left && res.push(item.left)
            item.right && res.push(item.right)
        }
        level++
    }
    return result
};
levelOrder(tree)
// [[1],
// 	[2,3],
// 	[31,32]]
```

递归版本

![层次遍历递归](img/层次遍历递归.gif)