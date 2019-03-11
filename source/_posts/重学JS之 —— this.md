---
title: 重学JS之 —— this
date: 
tag:
categories:
---

> this 的定义：this 是在执行时确定的，它取决于函数的调用环境。

一个面试题就可以将上面的定义解释的非常清楚。

```bash
var a = 1;
var obj = {
   a: 2,
   prop: {
      a: 3,
      getValue: function() {
         return this.a;
      }
   }
};

console.log(obj.prop.getValue());

var value = obj.prop.getValue;

console.log(value());
```

<!-- more -->

-   `输出结果是什么？`

    // 3
    // 1

由于 this 不是定义时确定的，而是执行时确定的。所以只看代码 13 -- 22 行是得不出结果，要看函数调用的环境。obj.prop.getValue() 执行时，getValue 是作为 obj.prop 的函数被调用的。所以此时 this 指向 obj.prop。当 getValue 被抛到全局，被赋值到 value 时，this 指向全局环境。

-   `如何修改上面代码，使得 value()的执行结果为 3.`

    这里考察改变 this 指向的方法。有三种方法 bind/call/apply。

```bash
# bind
var newValue = value.bind(obj.prop)
newValue()
# call
console.log(value.call(obj.prop))
# apply
console.log(value.apply(obj.prop))
```
