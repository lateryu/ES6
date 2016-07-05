# ECMAScript 6 入门

##1, let和const 命令
#####1, let 命令
 基本用法:
  ES6中 le用来声明变量,用法类是var,但是其声明的变量有作用域得概念,只在let命令所在的代码块内有效。
  <pre>
  {
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
  </pre>

1. for循环的计数器，就很合适使用let命令。
2. 不存在变量提升
3. 暂时性死区
  
只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响
<pre>
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
</pre>
上面代码中，存在全局变量tmp，但是块级作用域内let又声明了一个局部变量tmp，导致后者绑定这个块级作用域，所以在let声明变量前，对tmp赋值会报错。
<pre>
	if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
</pre>
上面代码中，在let命令声明变量tmp之前，都属于变量tmp的“死区”。

4. 不允许重复声明

	let不允许在相同作用域内，重复声明同一个变量。
5. ES6的块级作用域
   let实际上为JavaScript新增了块级作用域。
<pre>
   function f1() {
     let n = 5;
     if (true) {
      let n = 10;
     }
     console.log(n); // 5
   }
</pre>
   
6. 块级作用域与函数声明

	ES5规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。
<pre>
	// 情况一
	if (true) {
 	 function f() {}
	}

	// 情况二
	try {
 	 function f() {}
	} catch(e) {
		}
	</pre>

	上面代码的两种函数声明，根据ES5的规定都是非法的。

	ES6引入了块级作用域，明确允许在块级作用域之中声明函数。

#####2, CONST
  const声明一个只读的常量。一旦声明，常量的值就不能改变。
  
1. const的作用域与let命令相同：只在声明所在的块级作用域内有效。
2. const命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
3. const声明的常量，也与let一样不可重复声明。






## 2. 变量的解构赋值
#### 1. 数组的解构赋值
1. ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构
<pre>
	var [a, b, c] = [1, 2, 3];
	let [foo, [[bar], baz]] = [1, [[2], 3]];
	foo // 1
	bar // 2
	baz // 3
		
	let [ , , third] = ["foo", "bar", "baz"];
	third // "baz"

	let [x, , y] = [1, 2, 3];
	x // 1
	y // 3

	let [head, ...tail] = [1, 2, 3, 4];
	head // 1
	tail // [2, 3, 4]

	let [x, y, ...z] = ['a'];
	x // "a"
	y // undefined
	z // []
	</pre>
	如果解构不成功，变量的值就等于undefined。

2. 另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。
<pre>
	let [x, y] = [1, 2, 3];
	x // 1
	y // 2

	let [a, [b], d] = [1, [2, 3], 4];
	a // 1
	b // 2
	d // 4 </pre>
	上面两个例子，都属于不完全解构，但是可以成功。
3. 如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。
<pre>
	// 报错
	let [foo] = 1;
	let [foo] = false;
	let [foo] = NaN;
	let [foo] = undefined;
	let [foo] = null;
	let [foo] = {};</pre>
	上面的表达式都会报错，因为等号右边的值，要么转为对象以后不具备Iterator接口（前五个表达式），要么本身就不具备Iterator接口（最后一个表达式）。
	
4. 解构赋值不仅适用于var命令，也适用于let和const命令。

#### 2. 默认值
1. 解构赋值允许指定默认值。
<pre>
var [foo = true] = [];
foo // true

[x, y = 'b'] = ['a']; // x='a', y='b'
[x, y = 'b'] = ['a', undefined]; // x='a', y='b'
</pre>
注意，ES6内部使用严格相等运算符（===），判断一个位置是否有值。所以，如果一个数组成员不严格等于undefined，默认值是不会生效的。
<pre>
var [x = 1] = [undefined];
x // 1

var [x = 1] = [null];
x // null
</pre>
上面代码中，如果一个数组成员是null，默认值就不会生效，因为null不严格等于undefined。

2. 默认值可以引用解构赋值的其他变量，但该变量必须已经声明。
<pre>let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError</pre>
上面最后一个表达式之所以会报错，是因为x用到默认值y时，y还没有声明。

#### 3. 对象的解构赋值

<code>对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。如果没有同名的属性,解构赋值的结果就是undefined</code>

<pre>
var { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

var { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined</pre>

2. 如果变量名与属性名不一致，必须写成下面这样。
<pre>var { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'</pre>

3. 这实际上说明，对象的解构赋值是下面形式的简写（参见《对象的扩展》一章）。

<pre>
<code>
	var { foo: baz } = { foo: "aaa", bar: "bbb" };
	baz // "aaa"
	foo // error: foo is not defined</code></pre>
	
注意，采用这种写法时，变量的声明和赋值是一体的。对于let和const来说，变量不能重新声明，所以一旦赋值的变量以前声明过，就会报错。
<pre>let foo;
let {foo} = {foo: 1}; // SyntaxError: Duplicate declaration "foo"

let baz;
let {bar: baz} = {bar: 1}; // SyntaxError: Duplicate declaration "baz"</pre>

3. 解构赋值可以嵌套
4. 解构赋值可以指定默认值
<pre>var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var { message: msg = "Something went wrong" } = {};
msg // "Something went wrong"
</pre>
	默认值生效的条件是,对象的属性值严格等于undefined,
	
<pre>var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
</pre>

#### 4. 字符串的解构赋值,数值布尔值的解构赋值
........

####5 函数参数的解构赋值
1. 函数的参数也可以是使用解构赋值

<pre>
function add([x, y]){
  return x + y;
}
add([1, 2]); // 3
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
</pre>
2.函数的解构也可以使用默认值;
<pre>function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]</pre>
注意，下面的写法会得到不一样的结果。
<pre>function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]</pre>
####6 不能使用圆括号
1. 变量声明语句中，不能带有圆括号。
	<pre>// 全部报错
var [(a)] = [1];
var {x: (c)} = {};
var ({x: c}) = {};
var {(x: c)} = {};
var {(x): c} = {};}
var { o: ({ p: p }) } = { o: { p: 2 } };</pre>
2. 函数参数中，模式不能带有圆括号。
 <pre>
// 报错
function f([(z)]) { return z; }</pre>
3. 赋值语句中，不能将整个模式，或嵌套模式中的一层，放在圆括号之中。
	 <pre>// 全部报错
({ p: a }) = { p: 42 };
([a]) = [5];</pre>

#####7 解构赋值的用途
1. 交换变量
	<pre>
[x, y] = [y, x];</pre>
2. 从函数返回多值
	<pre>// 返回一个数组
function example() {
  return [1, 2, 3];
}
var [a, b, c] = example();
// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
var { foo, bar } = example();</pre>
3. 函数的参数定义
	<pre>// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);
// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});</pre>
4. 提取json
	<pre>var jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};
let { id, status, data: number } = jsonData;
console.log(id, status, number);
// 42, "OK", [867, 5309]
</pre>
5. 函数参数的默认值
	<pre>jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};</pre>
6. 遍历Maphi结构
任何部署了Iterator接口的对象，都可以用for...of循环遍历。Map结构原生支持Iterator接口，配合变量的解构赋值，获取键名和键值就非常方便。

	<pre>var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');
for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world</pre>

##