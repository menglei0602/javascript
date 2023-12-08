# Airbnb JavaScript 风格指南() 

注意：本指南假设您使用Babel，并要求您使用babel-preset-airbnb或同等产品。它还假设您正在应用程序中安装 shims/polyfills，使用airbnb-browser-shims或类似工具。

## 类型
- 1.1 基元：当您访问基元类型时，您直接处理它的值。

string
number
boolean
null
undefined
symbol
bigint

const foo = 1;
let bar = foo;

bar = 9;

console.log(foo, bar); // => 1, 9
符号和 BigInt 无法忠实地填充，因此在针对本身不支持它们的浏览器/环境时不应使用它们。

- 1.2 复杂：当您访问复杂类型时，您将处理对其值的引用。

object
array
function

const foo = [1, 2];
const bar = foo;

bar[0] = 9;

console.log(foo[0], bar[0]); // => 9, 9


## 参考

- 使用let、const（重新分配引用）块作用域代替var，避免使用var函数作用域。

```javascript
// const and let only exist in the blocks they are defined in.
{
  let a = 1;
  const b = 1;
  var c = 1;
}
console.log(a); // ReferenceError
console.log(b); // ReferenceError
console.log(c); // Prints 1
```
在上面的代码中，您可以看到引用a会b产生一个ReferenceError，同时c包含数字。这是因为a、b是块作用域，而 c是包含函数的作用域。

## 对象

- 3.1使用字面语法创建对象。eslint：no-new-object

```javascript
// bad
const item = new Object();

// good
const item = {};
```

- 3.2创建具有动态属性名称的对象时，使用计算属性名称。

> 为什么？它们允许您在一处定义对象的所有属性。

```javascript
function getKey(k) {
  return `a key named ${k}`;
}

// bad
const obj = {
  id: 5,
  name: 'San Francisco',
};
obj[getKey('enabled')] = true;

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
};
```

- 3.3使用对象方法简写。eslint：object-shorthand

```javascript
// bad
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```

- 3.4使用属性值简写。eslint：object-shorthand

> 为什么？它更短且具有描述性。

```javascript
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```

- 3.5在对象声明的开头对速记属性进行分组。

> 为什么？使用简写更容易辨别哪些属性。

```javascript
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

- 3.6仅引用无效标识符的属性。eslint：quote-props

> 为什么？一般来说，我们主观上认为它更容易阅读。它改进了语法高亮，并且也更容易被许多 JS 引擎优化。

```javascript
// bad
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
};

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

- 3.7不要Object.prototype直接调用 、hasOwnProperty、propertyIsEnumerable、 等方法isPrototypeOf。eslint：no-prototype-builtins

> 为什么？这些方法可能会受到相关对象的属性的影响 - 考虑一下{ hasOwnProperty: false }- 或者，该对象可能是空对象 ( Object.create(null))。在支持 ES2022 或具有诸如https://npmjs.com/object.hasown之类的 polyfill 的现代浏览器中，Object.hasOwn也可以用作Object.prototype.hasOwnProperty.call.

```javascript
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// better
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
console.log(has.call(object, key));

// best
console.log(Object.hasOwn(object, key)); // only supported in browsers that support ES2022

/* or */
import has from 'has'; // https://www.npmjs.com/package/has
console.log(has(object, key));
/* or */
console.log(Object.hasOwn(object, key)); // https://www.npmjs.com/package/object.hasown
```
---待看
- 3.8优先使用对象扩展语法而不是Object.assign浅复制对象。使用对象剩余参数语法来获取省略某些属性的新对象。eslint：prefer-object-spread

```javascript
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
delete copy.a; // so does this

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

## 数组

- 4.1使用字面语法创建数组。eslint：no-array-constructor

```javascript
// bad
const items = new Array();

// good
const items = [];
```

- 4.2使用Array#push而不是直接赋值来将项目添加到数组中。

```javascript
const someStack = [];

// bad
someStack[someStack.length] = 'abracadabra';

// good
someStack.push('abracadabra');
```

- 4.3使用数组扩展...来复制数组。

```javascript
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

- 4.4要将可迭代对象转换为数组，请使用 Spread...而不是Array.from

```javascript
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

- 4.5用于Array.from将类似数组的对象转换为数组。

```javascript
const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };

// bad
const arr = Array.prototype.slice.call(arrLike);

// good
const arr = Array.from(arrLike);
```

- 4.6使用Array.from而不是扩展...来映射可迭代对象，因为它避免了创建中间数组。

```javascript
// bad
const baz = [...foo].map(bar);

// good
const baz = Array.from(foo, bar);
```

- 4.7在数组方法回调中使用 return 语句。如果函数体由返回一个没有副作用的表达式的单个语句组成，则可以省略 return，如下8.2所示。eslint：array-callback-return

```javascript
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => x + 1);

// bad - no returned value means `acc` becomes undefined after the first iteration
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
});

// good
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
  return flatten;
});

// bad
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});

// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }

  return false;
});
```

- 4.8如果数组有多行，则在左数组括号之后和右数组括号之前使用换行符

```javascript
// bad
const arr = [
  [0, 1], [2, 3], [4, 5],
];

const objectInArray = [{
  id: 1,
}, {
  id: 2,
}];

const numberInArray = [
  1, 2,
];

// good
const arr = [[0, 1], [2, 3], [4, 5]];

const objectInArray = [
  {
    id: 1,
  },
  {
    id: 2,
  },
];

const numberInArray = [
  1,
  2,
];
```

## 解构

- 5.1访问和使用对象的多个属性时使用对象解构。eslint：prefer-destructuring

> 为什么？解构使您无需为这些属性创建临时引用，也无需重复访问该对象。重复对象访问会产生更多重复代码，需要更多阅读，并产生更多出错机会。解构对象还提供了块中使用的对象结构的单一定义，而不需要读取整个块来确定使用的内容。

```javascript
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

- 5.2使用数组解构。eslint：prefer-destructuring

```javascript
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```

- 5.3对多个返回值使用对象解构，而不是数组解构。

> 为什么？您可以随着时间的推移添加新属性或更改事物的顺序，而不会破坏调用站点。

```javascript
// bad
function processInput(input) {
  // then a miracle occurs
  return [left, right, top, bottom];
}

// the caller needs to think about the order of return data
const [left, __, top] = processInput(input);

// good
function processInput(input) {
  // then a miracle occurs
  return { left, right, top, bottom };
}

// the caller selects only the data they need
const { left, top } = processInput(input);
```

## 字符串

- 6.1对字符串使用单引号''。eslint：quotes

```javascript
// bad
const name = "Capt. Janeway";

// bad - template literals should contain interpolation or newlines
const name = `Capt. Janeway`;

// good
const name = 'Capt. Janeway';
```

- 6.2导致行超过 100 个字符的字符串不应使用字符串连接跨多行写入。

> 为什么？损坏的字符串使用起来很痛苦，并且使代码的可搜索性降低。

```javascript
// bad
const errorMessage = 'This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.';

// bad
const errorMessage = 'This is a super long error that was thrown because ' +
  'of Batman. When you stop to think about how Batman had anything to do ' +
  'with this, you would get nowhere fast.';

// good
const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
```

- 6.3当以编程方式构建字符串时，使用模板字符串而不是串联。eslint：prefer-template template-curly-spacing

> 为什么？模板字符串为您提供可读、简洁的语法，并具有适当的换行符和字符串插值功能。

```javascript
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// bad
function sayHi(name) {
  return `How are you, ${ name }?`;
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

- 6.4 eval()切勿在字符串上使用；它会出现太多的漏洞。eslint：no-eval

- 6.5 不要对字符串中不必要的字符进行转义。eslint：no-useless-escape

> 为什么？反斜杠会损害可读性，因此仅在必要时才应使用反斜杠。

```javascript
// bad
const foo = '\'this\' \i\s \"quoted\"';

// good
const foo = '\'this\' is "quoted"';
const foo = `my name is '${name}'`;
```

## 函数

- 7.5切勿命名参数arguments。这将优先于arguments赋予每个函数作用域的对象。
> 为什么？由于 JavaScript 允许函数有不定数目的参数，所以需要一种机制，可以在函数体内部读取所有参数。这就是`arguments`对象的由来。`arguments`对象包含了函数运行时的所有参数，`arguments[0]`就是第一个参数，`arguments[1]`就是第二个参数，以此类推。这个对象只有在函数体内部，才可以使用。
```javascript
// bad
function foo(name, options, arguments) {
  // ...
}

// good
function foo(name, options, args) {
  // ...
}
```

- 7.6切勿使用arguments，选择使用剩余语法...。eslint：prefer-rest-params

> 为什么？...明确说明您想要提取哪些参数。另外，剩余参数是一个真正的数组，而不仅仅是类似数组的arguments.

```javascript
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```

- 7.7使用默认参数语法而不是改变函数参数。

```javascript
// really bad
function handleThings(opts) {
  // No! We shouldn’t mutate function arguments.
  // Double bad: if opts is falsy it'll be set to an object which may
  // be what you want but it can introduce subtle bugs.
  opts = opts || {};
  // ...
}

// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
  // ...
}

// good
function handleThings(opts = {}) {
  // ...
}
```

- 7.8避免使用默认参数产生副作用。

> 为什么？它们的推理令人困惑。

```javascript
let b = 1;
// bad
function count(a = b++) {
  console.log(a);
}
count();  // 1
count();  // 2
count(3); // 3
count();  // 3
```

- 7.9始终将默认参数放在最后。eslint：default-param-last

```javascript
// bad
function handleThings(opts = {}, name) {
  // ...
}

// good
function handleThings(name, opts = {}) {
  // ...
}
```

- 7.10切勿使用 Function 构造函数来创建新函数。eslint：no-new-func

> 为什么？以这种方式创建函数会类似于计算字符串eval()，这会带来漏洞。

```javascript
// bad
const add = new Function('a', 'b', 'return a + b');

// still bad
const subtract = Function('a', 'b', 'return a - b');
```

- 7.11函数签名中的空格。eslint：space-before-function-paren space-before-blocks

> 为什么？一致性很好，并且在添加或删除名称时不必添加或删除空格。

```javascript
// bad
const f = function(){};
const g = function (){};
const h = function() {};

// good
const x = function () {};
const y = function a() {};
```

- 7.12切勿改变参数。eslint：no-param-reassign

> 为什么？操作作为参数传入的对象可能会在原始调用者中导致不需要的变量副作用。

```javascript
// bad
function f1(obj) {
  obj.key = 1;
}

// good
function f2(obj) {
  const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
}
```

- 7.14优先使用扩展语法...来调用可变参数函数。eslint：prefer-spread

> 为什么？它更干净，您不需要提供上下文，并且您不能轻松地使用 进行new组合apply。

```javascript
// bad
const x = [1, 2, 3, 4, 5];
console.log.apply(console, x);

// good
const x = [1, 2, 3, 4, 5];
console.log(...x);

// bad
new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));

// good
new Date(...[2016, 8, 5]);
```

- 7.15具有多行签名或调用的函数应像本指南中的所有其他多行列表一样缩进：每个项目单独占一行，最后一个项目后面带有逗号。eslint：function-paren-newline

```javascript
// bad
function foo(bar,
             baz,
             quux) {
  // ...
}

// good
function foo(
  bar,
  baz,
  quux,
) {
  // ...
}

// bad
console.log(foo,
  bar,
  baz);

// good
console.log(
  foo,
  bar,
  baz,
);
```

## 箭头功能
- 8.2如果函数体由返回一个没有副作用的表达式的单个语句组成，则省略大括号并使用隐式返回。否则，保留大括号并使用return语句。eslint：arrow-parens，arrow-body-style

> 为什么？语法糖。当多个函数链接在一起时，它读起来很好。

```javascript
// bad
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  `A string containing the ${nextNumber}.`;
});

// good
[1, 2, 3].map((number) => `A string containing the ${number + 1}.`);

// good
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  return `A string containing the ${nextNumber}.`;
});

// good
[1, 2, 3].map((number, index) => ({
  [index]: number,
}));

// No implicit return with side effects
function foo(callback) {
  const val = callback();
  if (val === true) {
    // Do something if callback returns true
  }
}

let bool = false;

// bad
foo(() => bool = true);

// good
foo(() => {
  bool = true;
});
```

- 8.3如果表达式跨越多行，请将其括在括号中以提高可读性。

为什么？它清楚地显示了函数的开始和结束位置。

```javascript
// bad
['get', 'post', 'put'].map((httpMethod) => Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
);

// good
['get', 'post', 'put'].map((httpMethod) => (
  Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
));
```

- 8.4为了清晰和一致，参数总是包含括号。eslint：arrow-parens

> 为什么？添加或删除参数时最大程度地减少差异改动。

```javascript
// bad
[1, 2, 3].map(x => x * x);

// good
[1, 2, 3].map((x) => x * x);

// bad
[1, 2, 3].map(number => (
  `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
));

// good
[1, 2, 3].map((number) => (
  `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
));

// bad
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

- 8.5避免混淆箭头函数语法 ( =>) 和比较运算符 ( <=, >=)。eslint：no-confusing-arrow


```javascript
// bad
const itemHeight = (item) => item.height <= 256 ? item.largeSize : item.smallSize;

// bad
const itemHeight = (item) => item.height >= 256 ? item.largeSize : item.smallSize;

// good
const itemHeight = (item) => (item.height <= 256 ? item.largeSize : item.smallSize);

// good
const itemHeight = (item) => {
  const { height, largeSize, smallSize } = item;
  return height <= 256 ? largeSize : smallSize;
};
```
## 类和构造函数

- 9.1始终使用class. 避免直接操纵prototype。

> 为什么？class语法更简洁，更容易推理。

```javascript
// bad
function Queue(contents = []) {
  this.queue = [...contents];
}
Queue.prototype.pop = function () {
  const value = this.queue[0];
  this.queue.splice(0, 1);
  return value;
};

// good
class Queue {
  constructor(contents = []) {
    this.queue = [...contents];
  }
  pop() {
    const value = this.queue[0];
    this.queue.splice(0, 1);
    return value;
  }
}
```

- 9.2用于extends继承。

> 为什么？它是一种在不破坏 的情况下继承原型功能的内置方法instanceof。

```javascript
// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function () {
  return this.queue[0];
};

// good
class PeekableQueue extends Queue {
  peek() {
    return this.queue[0];
  }
}
```

- 9.3方法可以返回this以帮助方法链接。

```javascript
// bad
Jedi.prototype.jump = function () {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function (height) {
  this.height = height;
};

const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined

// good
class Jedi {
  jump() {
    this.jumping = true;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }
}

const luke = new Jedi();

luke.jump()
  .setHeight(20);
```

- 9.7类方法应该使用this或做成静态方法，除非外部库或框架需要使用特定的非静态方法。作为实例方法应该表明它的行为根据接收者的属性而有所不同。eslint：class-methods-use-this

```javascript
// bad
class Foo {
  bar() {
    console.log('bar');
  }
}

// good - this is used
class Foo {
  bar() {
    console.log(this.bar);
  }
}

// good - constructor is exempt
class Foo {
  constructor() {
    // ...
  }
}

// good - static methods aren't expected to use this
class Foo {
  static bar() {
    console.log('bar');
  }
}
```
## 模块

- 10.1始终在非标准模块系统上使用模块 ( import/ )。export您始终可以转换为您喜欢的模块系统。

> 为什么？模块是未来，让我们现在就开始使用未来。

```javascript
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;

// best
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

- 10.2不要使用通配符导入。

> 为什么？这可确保您有一个默认导出。

```javascript
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';

// good
import AirbnbStyleGuide from './AirbnbStyleGuide';
```

- 10.3并且不要直接从导入导出。

> 为什么？尽管一行语句很简洁，但采用一种清晰的导入方式和一种清晰的导出方式可以使事情保持一致。

```javascript
// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';

// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

- 10.4只从一个地方的路径导入。eslint：no-duplicate-imports

> 为什么？从同一路径导入多行会使代码更难维护。

```javascript
// bad
import foo from 'foo';
// … some other imports … //
import { named1, named2 } from 'foo';

// good
import foo, { named1, named2 } from 'foo';

// good
import foo, {
  named1,
  named2,
} from 'foo';
```

- 10.5不要导出可变绑定。eslint：import/no-mutable-exports

> 为什么？一般来说，应该避免突变，尤其是在导出可变绑定时。虽然某些特殊情况可能需要此技术，但一般来说，只应导出常量引用。

```javascript
// bad
let foo = 3;
export { foo };

// good
const foo = 3;
export { foo };
```

- 10.6在具有单个导出的模块中，优先选择默认导出而不是命名导出。eslint：import/prefer-default-export

> 为什么？鼓励更多只导出一项内容的文件，这更有利于可读性和可维护性。

```javascript
// bad
export function foo() {}

// good
export default function foo() {}
```

- 10.7将以上所有内容放入import非导入声明中。eslint：import/first

> 为什么？由于imports 是提升的，因此将它们全部放在顶部可以防止出现意外的行为。

```javascript
// bad
import foo from 'foo';
foo.init();

import bar from 'bar';

// good
import foo from 'foo';
import bar from 'bar';

foo.init();
```

- 10.8多行导入应该像多行数组和对象文字一样缩进。eslint：object-curly-newline

> 为什么？大括号遵循与样式指南中所有其他大括号块相同的缩进规则，尾随逗号也是如此。

```javascript
// bad
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';

// good
import {
  longNameA,
  longNameB,
  longNameC,
  longNameD,
  longNameE,
} from 'path';
```

- 10.9模块导入语句中不允许使用 Webpack 加载器语法。eslint：import/no-webpack-loader-syntax

> 为什么？由于在导入中使用 Webpack 语法会将代码耦合到模块捆绑器。更喜欢使用 中的加载器语法webpack.config.js。

```javascript
// bad
import fooSass from 'css!sass!foo.scss';
import barCss from 'style!css!bar.css';

// good
import fooSass from 'foo.scss';
import barCss from 'bar.css';
```

- 10.10不要包含 JavaScript 文件扩展名 eslint：import/extensions

> 为什么？包含扩展会抑制重构，并且会对您在每个使用者中导入的模块的实现细节进行不适当的硬编码。

```javascript
// bad
import foo from './foo.js';
import bar from './bar.jsx';
import baz from './baz/index.jsx';

// good
import foo from './foo';
import bar from './bar';
import baz from './baz';
```

## 迭代器和生成器

- 11.1不要使用迭代器。更喜欢 JavaScript 的高阶函数而不是像for-inor之类的循环for-of。eslint：no-iterator no-restricted-syntax

> 为什么？这强制执行了我们不变的规则。处理返回值的纯函数比副作用更容易推理。 使用map()// every()/ filter()/ find()/ findIndex()/ reduce()/ some()... 迭代数组，并使用//Object.keys()生成数组，以便您可以迭代对象。Object.values()Object.entries()

```javascript
const numbers = [1, 2, 3, 4, 5];

// bad
let sum = 0;
for (let num of numbers) {
  sum += num;
}
sum === 15;

// good
let sum = 0;
numbers.forEach((num) => {
  sum += num;
});
sum === 15;

// best (use the functional force)
const sum = numbers.reduce((total, num) => total + num, 0);
sum === 15;

// bad
const increasedByOne = [];
for (let i = 0; i < numbers.length; i++) {
  increasedByOne.push(numbers[i] + 1);
}

// good
const increasedByOne = [];
numbers.forEach((num) => {
  increasedByOne.push(num + 1);
});

// best (keeping it functional)
const increasedByOne = numbers.map((num) => num + 1);
```

- 11.2暂时不要使用发电机。

> 为什么？它们不能很好地转译为 ES5。


- 11.3如果您必须使用生成器，或者您无视我们的建议，请确保它们的函数签名间隔正确。eslint：generator-star-spacing

> 为什么？function和*是同一概念关键字的一部分 -*不是 的修饰符function，function*是一个独特的构造，不同于function。

```javascript
// bad
function * foo() {
  // ...
}

// bad
const bar = function * () {
  // ...
};

// bad
const baz = function *() {
  // ...
};

// bad
const quux = function*() {
  // ...
};

// bad
function*foo() {
  // ...
}

// bad
function *foo() {
  // ...
}

// very bad
function
*
foo() {
  // ...
}

// very bad
const wat = function
*
() {
  // ...
};

// good
function* foo() {
  // ...
}

// good
const foo = function* () {
  // ...
};
```

## 特性

- 12.1访问属性时使用点表示法。eslint：dot-notation

```javascript
const luke = {
  jedi: true,
  age: 28,
};

// bad
const isJedi = luke['jedi'];

// good
const isJedi = luke.jedi;
```

- 12.2[]使用变量访问属性时使用括号表示法。

```javascript
const luke = {
  jedi: true,
  age: 28,
};

function getProp(prop) {
  return luke[prop];
}

const isJedi = getProp('jedi');
```

- 12.3**计算幂时使用幂运算符。eslint：prefer-exponentiation-operator.

```javascript
// bad
const binary = Math.pow(2, 10);

// good
const binary = 2 ** 10;
```

## 变量

- 13.1始终使用const或let来声明变量。不这样做会导致全局变量。我们希望避免污染全局命名空间。星球船长警告我们这一点。eslint：no-undef prefer-const
```javascript
// bad
superPower = new SuperPower();

// good
const superPower = new SuperPower();
```

- 13.5不要链接变量赋值。eslint：no-multi-assign

> 为什么？链接变量赋值创建隐式全局变量。

```javascript
// bad
(function example() {
  // JavaScript interprets this as
  // let a = ( b = ( c = 1 ) );
  // The let keyword only applies to variable a; variables b and c become
  // global variables.
  let a = b = c = 1;
}());

console.log(a); // throws ReferenceError
console.log(b); // 1
console.log(c); // 1

// good
(function example() {
  let a = 1;
  let b = a;
  let c = a;
}());

console.log(a); // throws ReferenceError
console.log(b); // throws ReferenceError
console.log(c); // throws ReferenceError
```

- 13.6避免使用一元递增和递减（++, --）。埃斯林特no-plusplus

> 为什么？根据 eslint 文档，一元递增和递减语句会受到自动分号插入的影响，并且可能会在应用程序中递增或递减值时导致无提示错误。使用诸如代替或之num += 1类的语句来改变你的值也更具表现力。禁止一元递增和递减语句还可以防止您无意中预先递增/预先递减值，这也可能导致程序中出现意外行为。num++num ++

```javascript
// bad

const array = [1, 2, 3];
let num = 1;
num++;
--num;

let sum = 0;
let truthyCount = 0;
for (let i = 0; i < array.length; i++) {
  let value = array[i];
  sum += value;
  if (value) {
    truthyCount++;
  }
}

// good

const array = [1, 2, 3];
let num = 1;
num += 1;
num -= 1;

const sum = array.reduce((a, b) => a + b, 0);
const truthyCount = array.filter(Boolean).length;
```
- 13.7=避免在作业前后出现换行符。如果您的赋值违反了max-len，请将值括在括号中。埃斯林特operator-linebreak。

> 为什么？周围的换行符=可能会混淆赋值的值。

```javascript
// bad
const foo =
  superLongLongLongLongLongLongLongLongFunctionName();

// bad
const foo
  = 'superLongLongLongLongLongLongLongLongString';

// good
const foo = (
  superLongLongLongLongLongLongLongLongFunctionName()
);

// good
const foo = 'superLongLongLongLongLongLongLongLongString';
```

## 提升
- 14.5变量、类和函数应在使用前定义。eslint：no-use-before-define

> 为什么？当变量、类或函数在使用后声明时，可能会损害可读性，因为读者不知道引用的是什么。对于读者来说，在遇到事物的使用之前，首先遇到事物的来源（无论是从另一个模块导入，还是在文件中定义）会更清楚。

```javascript
// bad

// Variable a is being used before it is being defined.
console.log(a); // this will be undefined, since while the declaration is hoisted, the initialization is not
var a = 10;

// Function fun is being called before being defined.
fun();
function fun() {}

// Class A is being used before being defined.
new A(); // ReferenceError: Cannot access 'A' before initialization
class A {
}

// `let` and `const` are hoisted, but they don't have a default initialization.
// The variables 'a' and 'b' are in a Temporal Dead Zone where JavaScript
// knows they exist (declaration is hoisted) but they are not accessible
// (as they are not yet initialized).

console.log(a); // ReferenceError: Cannot access 'a' before initialization
console.log(b); // ReferenceError: Cannot access 'b' before initialization
let a = 10;
const b = 5;


// good

var a = 10;
console.log(a); // 10

function fun() {}
fun();

class A {
}
new A();

let a = 10;
const b = 5;
console.log(a); // 10
console.log(b); // 5
```

## 比较运算符和相等

- 15.1使用===和!==，不要使用==和!=。eslint：eqeqeq

- 15.2条件语句（例如if语句）使用ToBoolean抽象方法的强制来评估其表达式，并始终遵循以下简单规则：

- 对象为true
- 未定义的为false
- Null为false
- 布尔值为布尔值
- 如果+0、-0 或 NaN ，则数字为false，否则为true
- 如果字符串为空字符串，则计算结果为false''，否则为true
```javascript
if ([0] && []) {
  // true
  // an array (even an empty one) is an object, objects will evaluate to true
}
```

- 15.3对布尔值使用快捷方式，但对字符串和数字使用显式比较。
```javascript
// bad
if (isValid === true) {
  // ...
}

// good
if (isValid) {
  // ...
}

// bad
if (name) {
  // ...
}

// good
if (name !== '') {
  // ...
}

// bad
if (collection.length) {
  // ...
}

// good
if (collection.length > 0) {
  // ...
}
```