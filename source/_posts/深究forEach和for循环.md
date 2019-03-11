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

### for、forEach、map 性能对比

##### 方法一 console.time()计算运行时间

主要是利用 console.time()和 console.timeEnd()方法，计算执行某个代码块所需要的时间。

```bash
var arr = [];

for (var i = 1; i <= 1000000; i++) {
    arr.push(i)
}

function a() {
    for (var i = 0; i < arr.length; i++) {}
};
console.time('a')
a();
console.timeEnd('a')


function b() {
    arr.forEach(function (e) {})
}
console.time('b')
b();
console.timeEnd('b')


function c() {
    arr.map(function (e) {})
}
console.time('c')
c();
console.timeEnd('c')

#取10次结果的平均数，得到 for的平均执行时间是1.65；forEach的平均执行时间是15；map的是19；
```

##### 方法二 Benchmark.js 开源库

```bash
#1.项目根目录下载benchmark依赖包。
npm i --save benchmark
#2.根据API添加要测试的代码段。
// app.js
var Benchmark = require('benchmark');
var suite = new Benchmark.Suite;
var arr = [];
for (var i = 1; i <= 1000000; i++) {
    arr.push(i)
}
// add tests
suite.add('for', function () {
        for (var i = 0; i < arr.length; i++) {}
    })
    .add('forEach', function () {
        arr.forEach(function (e) {})
    })
    .add('map', function () {
        arr.map(function (e) {})
    })
    // add listeners
    .on('cycle', function (event) {
        console.log(String(event.target));
    })
    .on('complete', function () {
        console.log('Fastest is ' + this.filter('fastest').map('name'));
    })
    // run async
    .run({
        'async': true
    });
#3.查看分析结果
for x 1,613 ops/sec ±0.82% (86 runs sampled)
forEach x 88.28 ops/sec ±3.36% (71 runs sampled)
map x 7.65 ops/sec ±2.11% (23 runs sampled)
Fastest is for
# ops/sec代表含义：测试结果以每秒钟执行测试代码的次数（Ops/sec）显示，这个数值越大越好；
# ±0.82%代表含义：测试过程中的统计误差，以及相对最好的慢了多少（%）。
```

> 注： 这种方法以我目前的知识还无法理解，对我有用的是，可以选出多种实现方式中的最优方案。

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
