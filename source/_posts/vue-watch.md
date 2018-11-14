---
title: vue watch 对象的新属性
? date
tags: ueditor
categories:
    - 前端
---

### 问题

在 data 中初始化一个对象 obj 为{}，业务要实现当点击某个按钮时，为 obj 添加一个属性 a = 1；可 watch 钩子函数并不能检测到 obj 的变化，即使是深度检测也不可以。只能在为 obj 添加完属性后，重新深拷贝下 obj，手动触发 watch 函数。代码如下。由此引发一个问题，vue 的 watch 监听机制到底是什么样的？

<!-- more -->

```
export default {
    data() {
        return {
            obj: {}
        }
    },
    watch: {
        obj: {
            handler(val) {
                console.log(val)
            },
            deep: true
        }
    },
    methods: {
        addProperty() {
            this.obj.a = 1
            this.obj = Object.assign({}, this.obj) // 添加本行后才会触发watch函数
        }
    }
}
```

### 探究 vue 的 watch 实现原理及实现方式

##### 预备知识：

**Object.defineProperty()**

> MDN 上的解释：直接为一个对象添加一个新的属性，或者修改该对象原有属性，并返回这个对象。

参数列表：

1.obj: 要操作的对象

2.prop: 要添加/修改的属性

3.descriptor: 要添加/修改的属性描述符

    descriptor包含以下：
      ```
      1.configurable: 为true时，descriptor的设置才能改变。默认不可随意修改false。

      2.value: 该属性对应的值。 默认undefined。

      3.enumerable: 为true时，该属性才是该对象的可列举属性之一。默认不可枚举fasle。

      4.writable: 为true时才可改变value。默认只读false。

      5.get: 访问器。writable为true时不可设置，否则报错。默认undefined。(拿出来细说)

      6.set:  访问器。writable为true时不可设置，否则报错。默认undefined。（拿出来细说）
      ```

**_descriptor 的 set 和 get 是实现 observe 的关键。_**

```
let o = {}
let value = 0
Object.defineProperty(o, 'a', {
    set: function(newValue) {
        value = newValue
    },
    get: function() {
        return value
    }
})
o.a = 1
console.log(o.a) // 1
```


