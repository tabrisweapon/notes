### 变量声明

let：es6为增加块级作用域而添加的语法，es6仍保留了var，兼容es5，当然应减少使用
变量死区：变量在声明前不能使用（与var的变量提升不同），而且子级的变量仍然能屏蔽上级变量，所以
```js
var shit； { /*dead zone here！no shit can be used*/ let shit;  }
```
const：字如其名，声明时必须初始化，过后不可改变，其他与let无异。当然，类似java，const作用于对象时，只能限制地址。

---
### 解构赋值
分两类数组与对象（毕竟也只有他俩有结构），当然字符串可以转为数组，布尔和数值也可以转成对象,解析时尽量不使用圆括号。
```js
// anything with Iterator interface can be transmitted
let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4

// besides you can define default value
let [foo = true] = [];
foo // true

// for Object
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
// what actually happened
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
// and if you wanna rename
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

```
你可以利用这个特性做的骚操作有：
```js
//exchange value
[x, y] = [y, x];

//function parameters
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);
// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});

//iterating map
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
```

---
### Arrow function
swift中也有类似语法。
```js
// you can define a function with
var sum = (num1, num2) => { return num1 + num2; }
// if you only have one parameter or no prar
var f = v => {return v};
var f = () => {return 5};
// if you only have one cmd in func body
const square = n => n * n;
```
这个语法有个caveat
```js
// create a new empty object for each puppy to play with
var chewToys = puppies.map(puppy => {});   // BUG!
var chewToys = puppies.map(puppy => ({})); // ok
```
上面这两句中，{}既可以看作是一个空object，也可以看做是一个block，然而看成是block的话，这个block却没有返回值。虽然有些傻逼，但js engine默认会把这东西看作是block而非object然后报个错。还有更白痴的：
```js
// create a new empty object for each puppy to play with
var chewToys = puppies.map(puppy => {key: value});   // BUG!
```
js engine会把这句看成是一个block里一个statement。所以这种情况总需要（）帮忙。

arrow func还解决了es5的一个问题
```js
{
  ...
  addAll: function addAll(pieces) {
    var self = this;
    _.each(pieces, function (piece) {
      self.add(piece);
    });
  },
  ...
}
```
上述代码之所以有这种写法，是因为如果不用self把this smuggle进来，实际调用addAll指向的不是上级对象而是window。而=>则会自动绑定this至上级对象。
```js
// ES6, no smuggle needed
{
  ...
  addAll: function addAll(pieces) {
    _.each(pieces, piece => this.add(piece));
  },
  ...
}
```
es6中应尽量使用=>，如果使用普通函数还想绑定this，需要借助Object.method()

BTW, ryfeng在最后还讲了下“尾递归”，挺有趣的。

---

### Object
属性和方法有了新的简写方式
```js
const foo = 'bar';
const baz = {foo};
baz // {foo: "bar"}
// 等同于
const baz = {foo: foo};

const o = {
  method() {
    return "Hello!";
  }
};
// 等同于
const o = {
  method: function() {
    return "Hello!";
  }
};
```
Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target），如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。Object.assign方法实行的是浅拷贝，而不是深拷贝。一些函数库提供Object.assign的定制版本（比如 Lodash 的_.defaultsDeep方法），可以得到深拷贝的合并。

#### 可枚举
对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。Object.getOwnPropertyDescriptor方法可以获取该属性的描述对象。
```js
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```
描述对象的enumerable属性，称为”可枚举性“，如果该属性为false，就表示某些操作会忽略当前属性。

目前，有四个操作会忽略enumerable为false的属性。

- for...in循环：只遍历对象自身的和继承的可枚举的属性。
- Object.keys()：返回对象自身的所有可枚举的属性的键名。
- JSON.stringify()：只串行化对象自身的可枚举的属性。
- Object.assign()： 忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性。

这四个操作之中，前三个是 ES5 就有的，最后一个Object.assign()是 ES6 新增的。其中，只有for...in会返回继承的属性，其他三个方法都会忽略继承的属性，只处理对象自身的属性。实际上，引入“可枚举”（enumerable）这个概念的最初目的，就是让某些属性可以规避掉for...in操作，不然所有内部属性和方法都会被遍历到。比如，对象原型的toString方法，以及数组的length属性，就通过“可枚举性”，从而避免被for...in遍历到。
```js
Object.getOwnPropertyDescriptor(Object.prototype, 'toString').enumerable
// false
Object.getOwnPropertyDescriptor([], 'length').enumerable
// false
```
上面代码中，toString和length属性的enumerable都是false，因此for...in不会遍历到这两个继承自原型的属性。

另外，ES6 规定，所有 Class 的原型的方法都是不可枚举的。
```js
Object.getOwnPropertyDescriptor(class {foo() {}}.prototype, 'foo').enumerable
// false
```
总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用for...in循环，而用Object.keys()代替。

Object.getOwnPropertyDescriptor方法会返回某个对象属性的描述对象（descriptor）。ES2017 引入了Object.getOwnPropertyDescriptors方法，返回指定对象所有自身属性（非继承属性）的描述对象。

----

### Class
本身是将prototype封装的Syntax Sugar
#### constructor
还是要使用this进行初始化，默认返回this，当让也可以手动指定

---

### Module
一个module内，this指向undefined

import & export只能放在文件的顶级作用域，以便静态优化

import语句是Singleton模式，反复引入和分别引入都没毛用
```js
import 'lodash';
import 'lodash';
//上面代码加载了两次lodash，但是只会执行一次。

import { foo } from 'my_module';
import { bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
```

export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。
```js
// 报错
export 1;

// 报错
var m = 1;
export m;
```
上述报错原因是后一句等同于签名，正确做法是：
```js
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```
但是我觉得这种报错略nc，应该改掉。

import的本质应该是执行模块文件后引入部分变量，屏蔽文件内的其他变量，类似传统浏览器中先执行某个js文件？？

由于 ES6 输入的模块变量，只是一个“符号连接”，所以这个变量是只读的，对它进行重新赋值会报错。
```js
// lib.js
export let obj = {};

// main.js
import { obj } from './lib';

obj.prop = 123; // OK
obj = {}; // TypeError
```

循环import应尽量避免，当迫不得已使用时，意味着模块间出现了强耦合（此时的多个模块本质是一个模块，只是进行了代码拆分）。这时候可能会出现如下情况
```js
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar);
export let foo = 'foo';

// b.mjs
import {foo} from './a';
console.log('b.mjs');
console.log(foo);
export let bar = 'bar';
```
上面代码中，a.mjs加载b.mjs，b.mjs又加载a.mjs，构成循环加载。执行a.mjs，结果如下
```
$ node --experimental-modules a.mjs
b.mjs
ReferenceError: foo is not defined
```
上面代码中，执行a.mjs以后会报错，foo变量未定义，这是为什么？

让我们一行行来看，ES6 循环加载是怎么处理的。首先，执行a.mjs以后，引擎发现它加载了b.mjs，因此会优先执行b.mjs，然后再执行a.js。接着，执行b.mjs的时候，已知它从a.mjs输入了foo接口，这时不会去执行a.mjs，而是认为这个接口已经存在了，继续往下执行。执行到第三行console.log(foo)的时候，才发现这个接口根本没定义，因此报错。

解决这个问题的方法，就是让b.mjs运行的时候，foo已经有定义了。这可以通过将foo写成函数来解决。
```js
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar());
function foo() { return 'foo' }
export {foo};

// b.mjs
import {foo} from './a';
console.log('b.mjs');
console.log(foo());
function bar() { return 'bar' }
export {bar};
```
这是因为函数具有提升作用，在执行import {bar} from './b'时，函数foo就已经有定义了，所以b.mjs加载的时候不会报错。这也意味着，如果把函数foo改写成函数表达式，也会报错。
```js
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar());
const foo = () => 'foo';
export {foo};
```
上面代码的第四行，改成了函数表达式，就不具有提升作用，执行就会报错.
