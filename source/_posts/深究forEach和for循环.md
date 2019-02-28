---
title: 深究forEach和for循环
date: 2018-05-10
tags: js
categories:
    - 前端
---

**今天无意发现了 forEach 和 for 循环的一个区别点，无脑使用了 forEach 这么多才发现。。之前在 forEach 中使用 return 也觉着怪怪的，只是没深究。今天拿出来研究一下。**

### 结论

> forEach 中使用 return，不会阻止循环的遍历。

### 验证

```bash
function a() {
    for (var i = 0; i < 3; i++) {
        if (i === 1) return
        console.log('ooooo----', i)
    }
};
a();
// 输出结果是 'ooooo———-' 1
```

<!-- more -->

```bash
function a() {
    var arr = [1, 2, 3];
    arr.forEach(item => {
        if (item === 2) return
        console.log(item)
    })
}
a();
// 依次输出结果 1 3
```

### 为什么会这样？

```bash

# forEach源码

Array.prototype.forEach = function(callback){
    for(var i = 0; i < this.length; i++) {
        callback(this[I], I, this)
    }
}
```

在执行 forEach 时，return 只是退出 callback 函数，并不能阻止继续循环。

### 总结

**forEach 并不是适用于所有场景的，必要时可以使用 for 循环。**

### 拓展

```bash
# Array.prototype.map 的实现
Array.prototype.myMap = function(callback) {
    Var arr = [];
    for(var I = 0; i < this.length; I++) {
        arr.push(callback(this[I], I, this))
    }
    return arr
}
```

### 反思

**谁都会用，但不一定都能消化。励志每天多消化一点。**

**know the how and endeavour to know the why.**
