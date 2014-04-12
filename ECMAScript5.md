# ECMAScript5 新增方法 #
---
描述：
本文档针对ECMAScript5扩展Array、String、Date、Function、Object、JSON的内容进行介绍
提纲：
![outline](http://10.10.52.199/sohuhd/tv/raw/master/doc/img/ECMAScript5_mmd.jpeg)

参考链接：
http://kangax.github.io/es5-compat-table/
http://www.zhangxinxu.com/wordpress/2012/01/introducing-ecmascript-5-1/
http://msdn.microsoft.com/zh-cn/library/htbw4ywd(v=vs.94).aspx

# [Array]() #
---
#### [Array.isArray]() ####
**描述：**
确定对象是否为数组。

**参数：**
object必需。 要测试的对象。

**备注：**
虽然instanceof 也能用于类型判断，但只适用于简单情况；多窗口情况下可能造成，一个对象将不可能是另外窗体中的构造函数实例，这样的混淆会造成instanceof不可靠。

**Polyfill**

```javascript
/**
 * Array.isArray
 * 备注：虽然instanceof 也能用于类型判断，但只适用于简单情况；多窗口情况下可能造成，一个对象将不可能是另外窗体中的构造函数实例，这样的混淆会造成instanceof不可靠
 * @uesd : 《javascript权威指南(第6版)》 P161
 * **/
Array.isArray = Array.isArray || function(o){
    return typeof o === "object" && Object.prototype.toString.call(o) === "[object Array]";
}
```

---
### [查询相关API]() ###
---
#### [Array.prototype.indexOf]() ###
**描述：**
返回某个值在数组中的第一个匹配项的索引。

**参数：**
array1.indexOf(searchElement[, fromIndex])

array1必需。 一个数组对象。
searchElement必需。 要在 array1 中定位的值。
fromIndex可选。 用于开始搜索的数组索引。 如果省略 fromIndex，则从索引 0 处开始搜索。支持负数，即从倒数第n向后找

**返回值：**
数组中的 searchElement 的第一个匹配项的索引；如果未找到 searchElement，则为 -1。

**Polyfill**

```javascript
/**
 * Array.prototype.indexOf
 * arr.indexOf(searchElement[, fromIndex])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *        https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.indexOf.js
 *        https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.indexOf) {
    Array.prototype.indexOf = function (searchElement, fromIndex) {
        if ( this === undefined || this === null ) {
            throw new TypeError( '"this" is null or not defined' );
        }

        var length = this.length >>> 0; // Hack to convert object.length to a UInt32

        fromIndex = +fromIndex || 0;

        if (Math.abs(fromIndex) === Infinity) {
            fromIndex = 0;
        }

        if (fromIndex < 0) {
            fromIndex += length;
            if (fromIndex < 0) {
                fromIndex = 0;
            }
        }

        for (;fromIndex < length; fromIndex++) {
            if (this[fromIndex] === searchElement) {
                return fromIndex;
            }
        }

        return -1;
    };
}

```



#### [Array.prototype.lastIndexOf]() ####
**描述：**
返回指定的值在数组中的最后一个匹配项的索引。

**参数：**
array1.lastIndexOf(searchElement[, fromIndex])
array1必需。 一个数组对象。
searchElement必需。 要在 array1 中定位的值。
fromIndex可选。 用于开始搜索的数组索引。 如果省略 fromIndex，则搜索将从数组中的最后一个索引处开始。

**返回值：**
数组中的 searchElement 的最后一个匹配项的索引；如果未找到 searchElement，则为 -1。

**Polyfill**

```javascript
/**
 * Array.prototype.lastIndexOf
 * array1.lastIndexOf(searchElement[, fromIndex])
 * @used : https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.lastIndexOf.jsh
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *        https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf
 *        https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.lastIndexOf)
{
    Array.prototype.lastIndexOf = function(searchElement /*, fromIndex*/)
    {
        "use strict";

        if (this == null)
            throw new TypeError();

        var t = Object(this);
        var len = t.length >>> 0;
        if (len === 0)
            return -1;

        var n = len;
        if (arguments.length > 1)
        {
            n = Number(arguments[1]);
            if (n != n)
                n = 0;
            else if (n != 0 && n != Infinity && n != -Infinity)
                n = (n > 0 || -1) * Math.floor(Math.abs(n));
        }

        var k = n >= 0  ? Math.min(n, len - 1) : len - Math.abs(n);

        for (; k >= 0; k--){
            if (k in t && t[k] === searchElement)
                return k;
        }
        return -1;
    };
}
```

---
### [判断相关API]() ###
---
#### [Array.prototype.every]() ####
**描述：**
确定数组的所有成员是否满足指定的测试。

**参数：**
array1.every(callbackfn[, thisArg])
array1必需。 一个数组对象。
callbackfn必需。callback(value, index, array) 一个接受最多三个参数的函数。 every 方法会为 array1 中的每个元素调用 callbackfn 函数，直到 callbackfn 返回 false，或直到到达数组的结尾。
thisArg可选。 可在 callbackfn 函数中为其引用 this 关键字的对象。 如果省略 thisArg，则 undefined 将用作 this 值。

**回调函数语法**
function callbackfn(value, index, array1)可使用最多三个参数来声明回调函数。

- value数组元素的值。
- index数组元素的数字索引。
- array1包含该元素的数组对象。

**返回值：**
如果 callbackfn 函数为所有数组元素返回 true，则为 true；否则为 false。 如果数组没有元素，则 every 方法将返回 true。

**示例：**

```javascript
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [12, 5, 8, 130, 44].every(isBigEnough);
// passed is false
passed = [12, 54, 18, 130, 44].every(isBigEnough);
// passed is true
```

**Polyfill**

```javascript
/**
 * Array.prototype.every
 * array1.every(callbackfn[, thisArg])
 * @used : https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.every.js
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.every)
{
    Array.prototype.every = function(callbackfn /*, thisArg */)
    {
        'use strict';

        if (this === void 0 || this === null)
            throw new TypeError();

        var t = Object(this);
        var len = t.length >>> 0;
        if (typeof callbackfn !== 'function')
            throw new TypeError();

        var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
        for (var i = 0; i < len; i++)
        {
            if (i in t && !callbackfn.call(thisArg, t[i], i, t))
                return false;
        }

        return true;
    };
}
```

#### [Array.prototype.some]() ####
**描述：**
有任意元素满足回调函数条件则返回 true。

**参数：**
array1.some(callbackfn[, thisArg])
array1必需。 一个数组对象。
callbackfn必需。 一个接受最多三个参数的函数。 every 方法会为 array1 中的每个元素调用 callbackfn 函数，直到 callbackfn 返回 false，或直到到达数组的结尾。
thisArg可选。 可在 callbackfn 函数中为其引用 this 关键字的对象。 如果省略 thisArg，则 undefined 将用作 this 值。

**回调函数语法**
function callbackfn(value, index, array1)可使用最多三个参数来声明回调函数。

- value数组元素的值。
- index数组元素的数字索引。
- array1包含该元素的数组对象。

**返回值：**
如果 callbackfn 函数为任何数组元素均返回 true，则为 true；否则为 false。

**示例：**

```javascript
var a = [1,2,3,4,5,10];
a.some(function(){ return x % 2 === 0; }) // => true : 存在元素是偶数
a.some(isNaN); // => false ： a不包含非数值元素
```

**Polyfill**

```javascript
/**
 * Array.prototype.some
 * array1.some(callbackfn[, thisArg])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.some.js
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.some)
{
    Array.prototype.some = function(callbackfn /*, thisArg */)
    {
        'use strict';

        if (this === void 0 || this === null)
            throw new TypeError();

        var t = Object(this);
        var len = t.length >>> 0;
        if (typeof callbackfn !== 'function')
            throw new TypeError();

        var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
        for (var i = 0; i < len; i++)
        {
            if (i in t && callbackfn.call(thisArg, t[i], i, t))
                return true;
        }

        return false;
    };
}
```

---
### [遍历相关API]() ###
---

#### [Array.prototype.forEach]() ####
**描述：**
为数组中的每个元素执行指定操作。

**参数：**
array1.forEach(callbackfn[, thisArg])
array1必需。 一个数组对象。
callbackfn必需。 一个接受最多三个参数的函数。 对于数组中的每个元素，forEach 都会调用 callbackfn 函数一次。
thisArg可选。 可在 callbackfn 函数中为其引用 this 关键字的对象。 如果省略 thisArg，则 undefined 将用作 this 值。

**回调函数语法**
function callbackfn(value, index, array1)可使用最多三个参数来声明回调函数。

- value数组元素的值。
- index数组元素的数字索引。
- array1包含该元素的数组对象。

**备注**
对于数组中的每个元素，forEach 方法都会调用 callbackfn 函数一次（采用升序索引顺序）。 数组中缺少的元素不会调用该回调函数。除了数组对象之外，forEach 方法可由具有 length 属性且具有已按数字编制索引的属性名的任何对象使用。

**修改数组对象**
forEach 方法不直接修改原始数组，但回调函数可能会修改它。 下表描述了在 forEach 方法启动后修改数组对象所获得的结果。

| forEach 方法启动后的条件 | 元素是否传递给回调函数 |
|-------|-------|
| 在数组的原始长度之外添加元素。 |  否。 |
| 添加元素以填充数组中缺少的元素。 | 是，如果该索引尚未传递给回调函数。 |
| 元素被更改。 | 是，如果该元素尚未传递给回调函数。 |
| 从数组中删除元素。 | 否，除非该元素已传递给回调函数。 |

**示例：**

```javascript
var numbers = [10, 11, 12];
var sum = 0;
numbers.forEach(
    function addNumber(value) { sum += value; }
);

document.write(sum);
// Output: 33

```

**Polyfill**

```javascript
/**
 * Array.prototype.forEach
 * array1.forEach(callbackfn[, thisArg])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.forEach.js
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.forEach)
{
    Array.prototype.forEach = function(callbackfn /*, thisArg */)
    {
        "use strict";

        if (this === void 0 || this === null)
            throw new TypeError();

        var t = Object(this);
        var len = t.length >>> 0;
        if (typeof callbackfn !== "function")
            throw new TypeError();

        var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
        for (var i = 0; i < len; i++)
        {
            if (i in t)
                callbackfn.call(thisArg, t[i], i, t);
        }
    };
}
```

#### [Array.prototype.map]() ####
**描述：**
对数组的每个元素调用定义的回调函数，并返回包含结果的新数组。

**参数：**
array1.forEach(callbackfn[, thisArg])
array1必需。 一个数组对象。
callbackfn必需。 一个接受最多三个参数的函数。 对于数组中的每个元素，forEach 都会调用 callbackfn 函数一次。
thisArg可选。 可在 callbackfn 函数中为其引用 this 关键字的对象。 如果省略 thisArg，则 undefined 将用作 this 值。

**返回值：**
与原始数组每一项有关联的，经过回调函数处理的新数组

**回调函数语法**
function callbackfn(value, index, array1)可使用最多三个参数来声明回调函数。

- value数组元素的值。
- index数组元素的数字索引。
- array1包含该元素的数组对象。


**备注**
对于数组中的每个元素，map 方法都会调用 callbackfn 函数一次（采用升序索引顺序）。 不为数组中缺少的元素调用该回调函数。除了数组对象之外，map 方法可由具有 length 属性且具有已按数字编制索引的属性名的任何对象使用。

**修改数组对象**
map 方法不直接修改原始数组，但回调函数可能会修改它。 下表描述了在 map 方法启动后修改数组对象所获得的结果。

| map 方法启动后的条件 | 元素是否传递给回调函数 |
|-------|-------|
| 在数组的原始长度之外添加元素。 |  否。 |
| 添加元素以填充数组中缺少的元素。 | 是，如果该索引尚未传递给回调函数。 |
| 元素被更改。 | 是，如果该元素尚未传递给回调函数。 |
| 从数组中删除元素。 | 否，除非该元素已传递给回调函数。 |

**示例：**

```javascript
// Apply Math.sqrt(value) to each element in an array.
var numbers = [9, 16];
var result = numbers.map(Math.sqrt);

document.write(result);
// Output: 3,4
```

**Polyfill**

```javascript
/**
 * Array.prototype.map
 * array1.for(callbackfn[, thisArg])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.map.js
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.map)
{
    Array.prototype.map = function(callbackfn /*, thisArg */)
    {
        "use strict";

        if (this === void 0 || this === null)
            throw new TypeError();

        var t = Object(this);
        var len = t.length >>> 0;
        if (typeof callbackfn !== "function")
            throw new TypeError();

        var res = new Array(len);
        var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
        for (var i = 0; i < len; i++)
        {
            // NOTE: Absolute correctness would demand Object.defineProperty
            //       be used.  But this method is fairly new, and failure is
            //       possible only if Object.prototype or Array.prototype
            //       has a property |i| (very unlikely), so use a less-correct
            //       but more portable alternative.
            if (i in t)
                res[i] = callbackfn.call(thisArg, t[i], i, t);
        }

        return res;
    };
}
```

---
### [筛选API]() ###
---
#### [Array.prototype.filter]() ####

**描述：**

返回数组中的满足回调函数中指定的条件的元素。

**参数：**
array1.filter(callbackfn[, thisArg])
array1必需。 一个数组对象。
callbackfn必需。 一个接受最多三个参数的函数。 对于数组中的每个元素，filter 方法都会调用 callbackfn 函数一次。
thisArg可选。 可在 callbackfn 函数中为其引用 this 关键字的对象。 如果省略 thisArg，则 undefined 将用作 this 值。

**返回值：**
一个包含回调函数为其返回 true 的所有值的新数组。 如果回调函数为 array1 的所有元素返回 false，则新数组的长度为 0。

**回调函数语法**
function callbackfn(value, index, array1)可使用最多三个参数来声明回调函数。

- value数组元素的值。
- index数组元素的数字索引。
- array1包含该元素的数组对象。


**备注**
对于数组中的每个元素，map 方法都会调用 callbackfn 函数一次（采用升序索引顺序）。 不为数组中缺少的元素调用该回调函数。除了数组对象之外，map 方法可由具有 length 属性且具有已按数字编制索引的属性名的任何对象使用。

**修改数组对象**
filter 方法不直接修改原始数组，但回调函数可能会修改它。 下表描述了在 filter 方法启动后修改数组对象所获得的结果。

| filter 方法启动后的条件 | 元素是否传递给回调函数 |
|-------|-------|
| 在数组的原始长度之外添加元素。 |  否。 |
| 添加元素以填充数组中缺少的元素。 | 是，如果该索引尚未传递给回调函数。 |
| 元素被更改。 | 是，如果该元素尚未传递给回调函数。 |
| 从数组中删除元素。 | 否，除非该元素已传递给回调函数。 |

**示例：**

```javascript

var arr = [5, "element", 10, "the", true];
var result = arr.filter(
    function (value) {
        return (typeof value === 'string');
    }
);

document.write(result);
// Output: element, the
```

**Polyfill**

```javascript
/**
 * Array.prototype.filter
 * array1.filter(callbackfn[, thisArg])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.filter.js
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if (!Array.prototype.filter)
{
    Array.prototype.filter = function(fun /*, thisArg */)
    {
        "use strict";

        if (this === void 0 || this === null)
            throw new TypeError();

        var t = Object(this);
        var len = t.length >>> 0;
        if (typeof fun != "function")
            throw new TypeError();

        var res = [];
        var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
        for (var i = 0; i < len; i++)
        {
            if (i in t)
            {
                var val = t[i];

                // NOTE: Technically this should Object.defineProperty at
                //       the next index, as push can be affected by
                //       properties on Object.prototype and Array.prototype.
                //       But that method's new, and collisions should be
                //       rare, so use the more-compatible alternative.
                if (fun.call(thisArg, val, i, t))
                    res.push(val);
            }
        }

        return res;
    };
}
```

---
### [将元素组合API]() ###
---
#### [Array.prototype.reduce]() ####

**描述：**
对数组中的所有元素调用指定的回调函数。 该回调函数的返回值为累积结果，并且此返回值在下一次调用该回调函数时作为参数提供。

**参数：**
array1.reduce(callbackfn[, initialValue])
array1必需。 一个数组对象。
callbackfn必需。 一个接受最多四个参数的函数。 对于数组中的每个元素，reduce 方法都会调用 callbackfn 函数一次。
initialValue可选。 如果指定 initialValue，则它将用作初始值来启动累积。 第一次调用 callbackfn 函数会将此值作为参数而非数组值提供。

**返回值：**
通过最后一次调用回调函数获得的累积结果。

**备注：**
如果提供了 initialValue，则 reduce 方法会对数组中的每个元素调用一次 callbackfn 函数（按升序索引顺序）。 如果未提供 initialValue，则 reduce 方法会对从第二个元素开始的每个元素调用 callbackfn 函数。回调函数的返回值在下一次调用回调函数时作为 previousValue 参数提供。 最后一次调用回调函数获得的返回值为 reduce 方法的返回值。数组中缺少的元素不会调用该回调函数。

**回调函数语法**
function callbackfn(previousValue, currentValue, currentIndex, array1)可使用最多四个参数来声明回调函数。

- previousValue 通过上一次调用回调函数获得的值。 如果向 reduce 方法提供 initialValue，则在首次调用函数时，previousValue 为 initialValue。
- currentValue 当前数组元素的值。
- currentIndex 当前数组元素的数字索引。
- array1 包含该元素的数组对象。

**示例：**

```javascript

var a = [1,2,3,4,5];
var merged = a.reduce(function(x,y){return x + y;},0);
console.log(merged); // => 15
```

**Polyfill**

```javascript
/**
 * Array.prototype.reduce
 * array1.reduce(callbackfn[, initialValue])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.reduce.js
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if ('function' !== typeof Array.prototype.reduce) {
    Array.prototype.reduce = function(callback, opt_initialValue){
        'use strict';
        if (null === this || 'undefined' === typeof this) {
            // At the moment all modern browsers, that support strict mode, have
            // native implementation of Array.prototype.reduce. For instance, IE8
            // does not support strict mode, so this check is actually useless.
            throw new TypeError(
                'Array.prototype.reduce called on null or undefined');
        }
        if ('function' !== typeof callback) {
            throw new TypeError(callback + ' is not a function');
        }
        var index, value,
            length = this.length >>> 0,
            isValueSet = false;
        if (1 < arguments.length) {
            value = opt_initialValue;
            isValueSet = true;
        }
        for (index = 0; length > index; ++index) {
            if (this.hasOwnProperty(index)) {
                if (isValueSet) {
                    value = callback(value, this[index], index, this);
                }
                else {
                    value = this[index];
                    isValueSet = true;
                }
            }
        }
        if (!isValueSet) {
            throw new TypeError('Reduce of empty array with no initial value');
        }
        return value;
    };
}
```

#### [Array.prototype.reduceRight]() ####

**描述：**
与Array.prototype.reduce原理一样，不同的是它按照索引从高到低(从右向左)处理数组。

**Polyfill**

```javascript
/**
 * Array.prototype.reduceRight
 * array1.reduceRight(callbackfn[, initialValue])
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight
 * @ref : https://github.com/plusdude/array-generics/blob/master/array.generics.js
 *       https://github.com/burib/es5-polyfills/blob/master/src/Array/Array.reduceRight.js
 *       https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 * **/
if ('function' !== typeof Array.prototype.reduceRight) {
    Array.prototype.reduceRight = function(callback, initialValue) {
        'use strict';
        if (null === this || 'undefined' === typeof this) {
            // At the moment all modern browsers, that support strict mode, have
            // native implementation of Array.prototype.reduceRight. For instance,
            // IE8 does not support strict mode, so this check is actually useless.
            throw new TypeError(
                'Array.prototype.reduceRight called on null or undefined');
        }
        if ('function' !== typeof callback) {
            throw new TypeError(callback + ' is not a function');
        }
        var index, value,
            length = this.length >>> 0,
            isValueSet = false;
        if (1 < arguments.length) {
            value = initialValue;
            isValueSet = true;
        }
        for (index = length - 1; -1 < index; --index) {
            if (this.hasOwnProperty(index)) {
                if (isValueSet) {
                    value = callback(value, this[index], index, this);
                }
                else {
                    value = this[index];
                    isValueSet = true;
                }
            }
        }
        if (!isValueSet) {
            throw new TypeError('Reduce of empty array with no initial value');
        }
        return value;
    };
}
```

---
# [String]() #
---

#### [String.prototype.trim]() ####
**描述：**
从字符串中移除前导空格、尾随空格和行结束符。

**参数：**
stringObj必需。 一个 String 对象或字符串。 trim 方法不修改该字符串。

**返回值：**
已移除前导空格、尾随空格和行结束符的字符串。

**Polyfill**

```javascript
/**
 * String.prototype.trim
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trim
 * **/
if (!String.prototype.trim) {
    String.prototype.trim = function () {
        return this.replace(/^\s+|\s+$/g, '');
    };
}
```

---
# [Date]() #
---
#### [Date.now]() ####
**描述：**
获取当前时间毫秒数

**Polyfill**

```javascript
/**
 * Date.prototype.now
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now
 * **/
if (!Date.now) {
    Date.now = function now() {
        return new Date().getTime();
    };
}
```

#### [Date.prototype.toISOString]() ####
**描述：**
以字符串值的形式返回采用 ISO 格式的日期。

**示例：**

```javascript
var dt = new Date("30 July 2010 15:05 UTC");
document.write(dt.toISOString());
document.write("<br />");
document.write(dt.toUTCString());

// Output:
//  2010-07-30T15:05:00.000Z
//  Fri, 30 Jul 2010 15:05:00 UTC
```

**Polyfill**

```javascript
/**
 * Date.prototype.toISOString
 * @used : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString
 * **/
if ( !Date.prototype.toISOString ) {
    ( function() {

        function pad(number) {
            if ( number < 10 ) {
                return '0' + number;
            }
            return number;
        }

        Date.prototype.toISOString = function() {
            return this.getUTCFullYear() +
                '-' + pad( this.getUTCMonth() + 1 ) +
                '-' + pad( this.getUTCDate() ) +
                'T' + pad( this.getUTCHours() ) +
                ':' + pad( this.getUTCMinutes() ) +
                ':' + pad( this.getUTCSeconds() ) +
                '.' + (this.getUTCMilliseconds() / 1000).toFixed(3).slice( 2, 5 ) +
                'Z';
        };

    }() );
}
```

---
# [Function]() #
---
#### [Function.prototype.bind]() ####
**描述：**
对于给定函数，创建具有与原始函数相同的主体的绑定函数。 在绑定功能中，this 对象解析为传入的对象。 该绑定函数具有指定的初始参数。

**参数：**
fn 必需。 一个函数对象。
thisArg 必需。 可在新函数中为其引用 this 关键字的对象。
arg1[,arg2[,argN]]] 可选。 要传递到新函数的参数的列表。

**返回值**
与 fn 函数相同的新函数，thisArg 对象和初始参数除外。

**示例：**

```javascript
function f(y){ retrun this.x + y;} //这个是待绑定的函数
var o = {x:1};                     //将要绑定的对象
var g = f.bind(o);                 //通过调用g(x)来调用o.f(x)
g(2) // => 3
```

**Polyfill**

```javascript
Function.prototype.bind = Function.prototype.bind || function(f,o){
	if(f.bind) return f.bind(o);
	else return function(){
		return f.apply(o,arguments);
	};
}
```

---
# [Object]() #

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)
参考：http://blog.csdn.net/shyleoking/article/details/7316174
---
#### [Object.create]() ####

**描述：**
该函数用来创建一个新的对象。新创建对象的内部属性[[Prototype]]的值由调用时的参数指定。在调用时还可以传入一个包含要定义的属性的对象。这个参数对象会被传递给 Object.defineProperties 函数来在新创建的对象中定义属性。通过 Object.create 可以很容易地实现基于原型的继承。

**参数：**
proto必需。要用作原型的对象。 可以为 null。
descriptors可选。 包含一个或多个属性描述符的 JavaScript 对象。

**返回值：**
一个具有指定的内部原型且包含指定的属性（如果有）的新对象。

**备注：**
descriptors对象“数据属性”是可获取且可设置值的属性。 数据属性描述符包含 value 特性，以及 writable、enumerable 和 configurable 特性。 如果未指定最后三个特性，则它们默认为 false。 只要检索或设置该值，“访问器属性”就会调用用户提供的函数。 访问器属性描述符包含 set 特性和/或 get 特性。 有关详细信息，请参阅 Object.defineProperty 函数 (JavaScript)。

**示例：**

```javascript

//下面的示例创建使用 null 原型的对象并添加两个可枚举的属性。

var newObj = Object.create(null, {
            size: {
                value: "large",
                enumerable: true
            },
            shape: {
                value: "round",
                enumerable: true
            }
        });

document.write(newObj.size + "<br/>");
document.write(newObj.shape + "<br/>");
document.write(Object.getPrototypeOf(newObj));

// Output:
// large
// round
// null
```

**Polyfill**

```javascript
/**
 * Object.create
 * 备注：mozilla和大部分git中的代码只支持第一个参数，并且不支持第一个参数为null的情况，所以firefox与其他浏览器在ES5 Object.create方法的实现上存在差异。目前的polyfill支持两个参数，且支持第一个参数为null的情况。
 * 注意：该方法中使用了ES5的 Object.defineProperties,所以在申明该方法前请确定Object.defineProperties方法已经存在，否则会抛出异常
 * @uesd : https://github.com/es-shims/es5-shim/blob/master/es5-sham.js
 * @ref : https://github.com/bullgare/polyfill5/blob/master/polyfill5.js
 *       https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create
 * **/
if (!Object.create) {

    // Contributed by Brandon Benvie, October, 2012
    var createEmpty;
    var supportsProto = Object.prototype.__proto__ === null;
    if (supportsProto || typeof document == 'undefined') {
        createEmpty = function () {
            return { "__proto__": null };
        };
    } else {
        // In old IE __proto__ can't be used to manually set `null`, nor does
        // any other method exist to make an object that inherits from nothing,
        // aside from Object.prototype itself. Instead, create a new global
        // object and *steal* its Object.prototype and strip it bare. This is
        // used as the prototype to create nullary objects.
        createEmpty = function () {
            var iframe = document.createElement('iframe');
            var parent = document.body || document.documentElement;
            iframe.style.display = 'none';
            parent.appendChild(iframe);
            iframe.src = 'javascript:';
            var empty = iframe.contentWindow.Object.prototype;
            parent.removeChild(iframe);
            iframe = null;
            delete empty.constructor;
            delete empty.hasOwnProperty;
            delete empty.propertyIsEnumerable;
            delete empty.isPrototypeOf;
            delete empty.toLocaleString;
            delete empty.toString;
            delete empty.valueOf;
            empty.__proto__ = null;

            function Empty() {}
            Empty.prototype = empty;
            // short-circuit future calls
            createEmpty = function () {
                return new Empty();
            };
            return new Empty();
        };
    }

    Object.create = function create(prototype, properties) {

        var object;
        function Type() {}  // An empty constructor.

        if (prototype === null) {
            object = createEmpty();
        } else {
            if (typeof prototype !== "object" && typeof prototype !== "function") {
                // In the native implementation `parent` can be `null`
                // OR *any* `instanceof Object`  (Object|Function|Array|RegExp|etc)
                // Use `typeof` tho, b/c in old IE, DOM elements are not `instanceof Object`
                // like they are in modern browsers. Using `Object.create` on DOM elements
                // is...err...probably inappropriate, but the native version allows for it.
                throw new TypeError("Object prototype may only be an Object or null"); // same msg as Chrome
            }
            Type.prototype = prototype;
            object = new Type();
            // IE has no built-in implementation of `Object.getPrototypeOf`
            // neither `__proto__`, but this manually setting `__proto__` will
            // guarantee that `Object.getPrototypeOf` will work as expected with
            // objects created using `Object.create`
            object.__proto__ = prototype;
        }

        if (properties !== void 0) {
            Object.defineProperties(object, properties);
        }

        return object;
    };
}
```

---
### [属性相关API]() ###
---
#### [Object.defineProperty]() ####


**描述：**
将属性添加到对象或修改现有属性的特性。

**参数：**
Object.defineProperty(object, propertyname, descriptor)
object必需。 对其添加或修改属性的对象。 这可以是本机 JavaScript 对象（即用户定义的对象或内置对象）或 DOM 对象。
propertyname必需。 一个包含属性名称的字符串。
descriptor必需。 属性的描述符。 它可以针对数据属性或访问器属性。

**返回值：**
已修改的对象。

**备注：**
可使用 Object.defineProperty 函数来执行下面的操作：

- 将新属性添加到对象。 在对象没有指定的属性名称时执行此操作。
- 修改现有属性的特性。 在对象已有指定的属性名称时执行此操作。

描述符对象中提供了属性定义，它描述了数据属性或访问器属性的特性。 描述符对象是 Object.defineProperty 函数的参数。
若要将多个属性添加到对象或要修改多个现有属性，可以使用 Object.defineProperties 函数 (JavaScript)。

**示例：**

```javascript
//添加属性
var obj = {};
Object.defineProperty(obj, "newDataProperty",{
        value: 101,
        writable: true,
        enumerable: true,
        configurable: true
    });


```

```javascript
//修改属性，接上面代码
Object.defineProperty(obj, "newDataProperty", { writable: false });

```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

#### [Object.defineProperties]() ####

**描述：**
将一个或多个属性添加到对象，并/或修改现有属性的特性。

**参数：**
object.defineProperties(object, descriptors)
object 必需。 对其添加或修改属性的对象。 这可以是本机 JavaScript 对象或 DOM 对象。
descriptors 必需。 包含一个或多个描述符对象的 JavaScript 对象。每个描述符对象描述一个数据属性或访问器属性。

**返回值：**
已传递给函数的对象。

**备注：**
descriptors参数是一个包含一个或多个描述符对象的对象。数据属性是可储存和检索值的属性。数据属性描述符包含一个 value 特性和/或一个writable特性。有关更多信息，请参见数据属性和访问器属性。 一旦设置或检索属性值，访问器属性就会调用用户提供的函数。 访问器属性描述符包含 set 特性和/或 get 特性。

**示例：**

```javascript
//添加属性
var obj = {};
Object.defineProperties(obj, {
    newDataProperty: {
        value: 101,
        writable: true,
        enumerable: true,
        configurable: true
    },
    newAccessorProperty: {
        set: function (x) {
        document.write("in property set accessor" + newLine);
        this.newaccpropvalue = x;
    },
    get: function () {
        document.write("in property get accessor" + newLine);
        return this.newaccpropvalue;
    },
    enumerable: true,
    configurable: true
}});
```

```javascript
//修改属性，接上面代码
Object.defineProperties(obj, {
    newDataProperty: { writable: false },
    newAccessorProperty: { enumerable: false },
    anotherDataProperty: { value: "abc" }
});
```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

#### [Object.getPrototypeOf]() ####

**描述：**
返回对象的原型。

**参数：**
Object.getPrototypeOf(object)
object必需。 引用原型的对象。

**返回值：**
object 参数的原型。 原型也是对象。

**示例：**

```javascript

//下面的示例使用 Object.getPrototypeOf 函数来验证数据类型。
var reg = /a/;
var result = (Object.getPrototypeOf(reg) === RegExp.prototype);
document.write(result + " ");

var err = new Error("an error");
var result = (Object.getPrototypeOf(err) === Error.prototype);
document.write(result);

// Output: true true
```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

#### [Object.getOwnPropertyDescriptor]() ####

**描述：**
获取指定对象自己的属性描述符。 自己的属性描述符是直接在对象上定义的描述符，而不是从对象的原型继承的描述符。

**参数：**
Object.getOwnPropertyDescriptor(object, propertyname)
object必需。包含该属性的对象。
propertyname必需。属性的名称。

**返回值：**
属性的描述符。

**示例：**

```javascript
//若要列出属性的特性，可将以下代码添加到该示例。
// Get the descriptor from the object.
var desc2 = Object.getOwnPropertyDescriptor(obj, "newDataProperty");

// List the descriptor attributes.
for (var prop in desc2) {
    document.write(prop + ': ' + desc2[prop]);
    document.write("<br />");
}

// Output:
// value: abc
// writable: false
// enumerable: true
// configurable: true
```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

#### [Object.getOwnPropertyNames]() ####

**描述：**
返回对象自己的属性的名称。 一个对象的自己的属性是指直接对该对象定义的属性，而不是从该对象的原型继承的属性。 对象的属性包括字段（对象）和函数。

**参数：**
Object.keys(object)
object必需。 包含自己的属性的对象

**返回值：**
一个数组，其中包含对象自己的属性的名称。

**示例：**

```javascript
//下面的示例创建一个对象，该对象具有三个属性和一个方法。 然后使用 getOwnPropertyNames 方法获取该对象自己的属性（包括方法）。
function Pasta(grain, width, shape) {
    // Define properties.
    this.grain = grain;
    this.width = width;
    this.shape = shape;
    this.toString = function () {
        return (this.grain + ", " + this.width + ", " + this.shape);
    }
}

// Create an object.
var spaghetti = new Pasta("wheat", 0.2, "circle");

// Get the own property names.
var arr = Object.getOwnPropertyNames(spaghetti);
document.write (arr);

// Output:
//   grain,width,shape,toString
```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

#### [Object.keys]() ####

**描述：**
返回对象的可枚举属性和方法的名称。

**参数：**
object必需。 包含属性和方法的对象。 这可以是您创建的对象或现有文档对象模型 (DOM) 对象。

**返回值：**
一个数组，其中包含对象的可枚举属性和方法的名称。

**备注：**
keys 方法仅返回可枚举属性和方法的名称。 若要返回可枚举的和不可枚举的属性和方法的名称，可使用 Object.getOwnPropertyNames 函数 (JavaScript)。

**示例：**

```javascript

//下面的示例创建一个对象，该对象具有三个属性和一个方法。 然后使用 keys 方法获取该对象的属性和方法。
// Create a constructor function.
function Pasta(grain, width, shape) {
    this.grain = grain;
    this.width = width;
    this.shape = shape;

    // Define a method.
    this.toString = function () {
        return (this.grain + ", " + this.width + ", " + this.shape);
    }
}

// Create an object.
var spaghetti = new Pasta("wheat", 0.2, "circle");

// Put the enumerable properties and methods of the object in an array.
var arr = Object.keys(spaghetti);
document.write (arr);

// Output:
// grain,width,shape,toString
```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

===
---
### [扩展相关API]() ###
---
===

#### [Object.seal]() ####

**描述：**
阻止修改现有属性的特性，并阻止添加新属性。

**参数：**
object必需。 在其上锁定特性的对象。

**返回值：**
传递给函数的对象。

**备注：**
Object.seal 函数执行以下两项操作：

- 使对象不可扩展，这样便无法向其添加新属性。
- 为对象的所有属性将 configurable 特性设置为 false。

在 configurable 特性为 false 时，无法更改属性的特性且无法删除属性。 在 configurable 为 false 且 writable 为 true 时，可以更改 value 和 writable 特性。
Object.seal 函数不更改 writable 特性。

**示例：**

```javascript
// Create an object that has two properties.
var obj = { pasta: "spaghetti", length: 10 };
// Seal the object.
Object.seal(obj);
document.write(Object.isSealed(obj));
document.write("<br/>");

// Try to add a new property, and then verify that it is not added. 
obj.newProp = 50;
document.write(obj.newProp);
document.write("<br/>");

// Try to delete a property, and then verify that it is still present. 
delete obj.length;
document.write(obj.length);

// Output:
// true
// undefined
// 10
```

**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)

===

#### [Object.seal相关函数]() ####
以下相关函数可阻止修改对象的特性。

| 函数 | 对象已设置为不可扩展的 | 为每个属性将 configurable 设置为 false | 为每个属性将 writable 设置为 false |
|-------|-------|-------|-------|
|  Object.preventExtensions | 是 | 否 | 否|
| Object.seal | 是 | 是 | 否 |
| Object.freeze | 是 | 是 | 是 |


如果满足下表中标记的所有条件，则以下函数返回 true。

| 函数 | 对象是否可扩展 | 为所有属性将 configurable 设置为 false | 为所有数据属性将 writable 设置为 false |
|-------|-------|-------|-------|
| Object.isExtensible | 是 | 否 | 否|
| Object.isSealed | 否 | 是 | 否 |
| Object.isFrozen | 否 | 是 | 是 |


**Polyfill**
描述：整个ES5中Object的polyfill请参考[es5-sham.js](https://github.com/es-shims/es5-shim/blob/master/es5-sham.js)


===
---
# [JSON]() #
---
#### [JSON.stringify]() ####

**描述：**
将 JavaScript 值转换为 JavaScript 对象表示法 (Json) 字符串。

**参数：**
JSON.stringify(value [, replacer] [, space])
value 必需。 要转换的 JavaScript 值（通常为对象或数组）。
replacer 可选。 转换结果的函数或数组。

- 如果 replacer 为一个函数，则 JSON.stringify 会调用该函数，并传入每个成员的键和值。 使用返回值而不是原始值。 如果此函数返回 undefined，则排除成员。 根对象的键是一个空字符串：""。
- 如果 replacer 为一个数组，则仅转换该数组中具有键值的成员。 成员的转换顺序与键在数组中的顺序一样。 当 value 参数也为数组时，将忽略 replacer 数组。

space 可选。 向返回值 JSON 文本添加缩进、空白和换行符以使其更易于读取。

- 如果省略 space，则将生成返回值文本，而没有任何额外空白。
- 如果 space 为一个数字，则返回值文本在每个级别缩进指定数目的空格。 如果 space 大于 10，则文本缩进 10 个空格。
- 如果 space 是非空字符串（例如“\t”），则返回值文本在每个级别中缩进字符串中的字符数。
- 如果 space 是长度大于 10 个字符的字符串，则使用前 10 个字符。

**Polyfill：**
参见 tv\js\base\core\g.js 1653 - 1696行实现

===

#### [JSON.parse]() ####

===

**描述：**
将 JavaScript 对象表示法 (JSON) 字符串转换为对象。
**参数：**
JSON.parse(text [, reviver])
text 必需。 一个有效的 JSON 字符串。
reviver 可选。 一个转换结果的函数。 将为对象的每个成员调用此函数。 如果成员包含嵌套对象，则先于父对象转换嵌套对象。 对于每个成员，会发生以下情况：

- 如果 reviver 返回一个有效值，则成员值将替换为转换后的值。
- 如果 reviver 返回它接收的相同值，则不修改成员值。
- 如果 reviver 返回 null 或 unde
- fined，则删除成员。


**Polyfill：**
参见 tv\js\base\core\g.js 1653 - 1696行实现