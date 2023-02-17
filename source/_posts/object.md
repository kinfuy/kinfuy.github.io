---
title: ES6之对象方法
theme: default
tag: Javascript
time: 2020.6.13
---

## ES6 之对象方法

### <a name="Object.is()">Object.is()</a>

与严格比较运算符（===）的行为基本一致。

```JS
Object.is('obj', 'obj')
// true
Object.is({}, {})
// false
```

不同之处只有两个：一是+0 不等于-0，二是 NaN 等于自身。

```JS
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

当然我们也可以用 ES5 的方法来实现该方法，原理还是利用严格运算符（===），只是特殊的情况进行特殊处理。

```JS
Object.defineProperty(Object, 'is', {
  value: function(x, y) {
    if (x === y) {
      // 针对+0 不等于 -0的情况
      return x !== 0 || 1 / x === 1 / y;
    }
    // 针对NaN的情况
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false,
  writable: true
});
```

### <a name="Object.assign()">Object.assign()</a>

Object.assign 方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。可以实现浅拷贝。（浅拷贝，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。）

```JS
let target = { a: 1 };

let source1 = { b: 2 };
let source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

注意：

    （0）：Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。
    （1）：如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性
    （2）：如果只有一个参数，Object.assign会直接返回该参数。
    （3）：如果该参数不是对象，则会先转成对象，然后返回。
    （4）：由于undefined和null无法转成对象，所以如果它们作为target，就会报错。如果非对象参数出现在source的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果undefined和null不在首参数，就不会报错。
    （5）：其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。

Object.assign 拷贝的属性是有限制的，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（enumerable: false）。

```JS
var target = { a: { b: 'c', d: 'e' } }
var source = { a: { b: 'hello' } }
Object.assign(target, source)
// { a: { b: 'hello' } }
```

对于这种嵌套的对象，一旦遇到同名属性，Object.assign 的处理方法是替换，而不是添加。

```JS
Object.assign([1, 2, 3], [4, 5])
// [4, 5, 3]
```

Object.assign 可以用来处理数组，但是会把数组视为对象。上面代码中，Object.assign 把数组视为属性名为 0、1、2 的对象，因此目标数组的 0 号属性 4 覆盖了原数组的 0 号属性 1。
