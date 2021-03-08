# HTML

# CSS

# JavaScript

# 算法

## 树

### 层次遍历

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

