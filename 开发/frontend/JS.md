# JavaScript

## feature

* high-level 
* garbage-collected
* interpreted or just-in-time compiled
* multi-paradigm 多范型
  * procedural programming
  * object-oriented programming
  * functional programming
* first-class functions
  * functions are simply treated as variables
    * we can pass them into other functions and return them from functions
* dynamics
  * dynamic type
* single-threaded
* non-blocking event loop
  * 因为是单线程，不能让某个task堵死程序，所以需要callback

## JS Engine and runtime

* compilation: 需要先转成机器码，之后才能运行
* interpretation：执行一行转一行
* just-in-time interpretation （JS Engine）: 转成机器码之后马上运行
  1. parsing
     * generated AST abstract syntax tree
  2. compilation 把AST转换成machine code
  3. execution 执行machine code

### runtime in the browser

* it is the container including all the things that we need to use javaScript (in this case in the browser)
  * JS engine
    * HEAP, 存variable的地方
    * call stack
  * web APIs
  * callback queue
    * event loop - essential for non-blocking concurrency model

### runtime in NodeJS

* JS Engine
  * HEAP, 存variable的地方
  * call stack
* C++ Bindings & Thread Pool
* callback queue
  * event loop - essential for non-blocking concurrency model

## Execution contexts and the call stack

* procedure
  1. creation of global execution context (for top-level code)
     * which is not inside a function

ES5 - ES6 - .... - ES11

ES就是JS的别名 ECMAScript

## tag

1. script



# fundamental

1. let, const, var

   * const 常量
   * let 变量
   * var 
   * 也可以不用声明关键词（会默认使用var），但是最好不要这样 (**在strict mode下不可以**)
     * 因为这样默认建立global variable

2. operators

   * = assignment operator
   * +-*/ 加减乘除
     * +可以用于合并str
     * += -= *= /=
     * ++ --
   * 2 ** 3 = 2 * 2 * 2 power
   * `< > <= >= ==`comparison operator

3. operator precedence

   * https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_precedence

4. Strings and Template literals

   * string concatenation `"AAA " + "BBB " + Variable` 

   * template 

     ```
     `AAA BBB ${ss}` 
     ```

5. type conversion and coercion

   * coercion 强迫
     * ‘1’ + 1 = ‘11’ **加号**会把数字强转str
     * ‘23’ - ‘10’ - 3 = 10 **减号**会把str强转数字

6. truthy and **falsy** values

   * 5 falsy value
     * 0， ‘’， undefined，null， NaN

7. === VS ==

   * === strict equal  '18' == 18 --> False
   * == **coercion** equal '18' == 18 --> True

8. prompt

   * 弹出框的输入框
   * `const input = prompt("how old are you?");`

9. logic operator

   * ! && ||

10. switch statement

    ```
    switch (statement){
    	case 'AAA':
    		...
    		break;
    	case 'BBB':
    		...
    		break;
    	default:
    		...
    }
    ```

11. statements and expressions

12. ternary operator

    `<statement> ? <true part>: <false part>`

13. strict mode

    `'use strict'` 打开就对了

    * 有两个好处 (让这个语言更加formal)
      1. 会禁止一部分行为
      2. 不会存在silent fail 的情况

14. function

    ```javascript
    // function declaration
    function <functionName>(<params>) {
    	...
    }
    
    // function expression
    const <functionName> = function (<params>) {
        ...
    }
    
    // Arrow function
    const <functionName> = (<params>) => {
        ...
    }
    ```

    * arrow function 是function expression的语法糖
    * function declaration 可以在声明前使用 (hoisting)
      * 但是我仍然不觉得这个是个好的选择

15. arrays

    ```js
    const <valName> = new Array(<params1>， )
    // or
    const <valName> = []
    // 甚至
    const <valName> = Array(<initial params>)
    ```

    * [Array, New Array(), []的区别和使用](https://www.jianshu.com/p/75a45851b655)

    ```
    .push() 结尾塞一个
    .unshift() 开头塞一个
    .pop() remove & return 结尾的一个
    .shift() remove & return 开头的一个
    .indexOf() return第一个匹配的element的index
    .includes() return是否存在匹配的element
    ```

16. objects

    ```js
    // declaration
    const <objName> = {
    	<key1>: <val1>,
    	<key2>: <val2>,
    	...
       <funcName>: function(){
           // 可以直接使用obj中的variable
           // 通过 this
           // 但是 arrow function 不可以用this
           console.log(this.<key1>)
       },
    }
    
    // retrieve
    .<keyName> // dot notation 不可拼接
    ['<keyName>'] // bracket notation 可以拼接
    
    
    ```

17. loop

    ```
    for(let i=0;i<10;i++){
    	...
    }
    for(let i of <array>) {
    	...
    }
    
    while() {
    	...
    }
    
    // break
    // continue
    ```

# Editor Setup

## VS Code

1. prettier

   1. formatting code

   * .pretierrc

     ```
     {
     	"singleQuote": true
     }
     ```

     

2. snippet

   1. 输入简写

   * file -> preference -> config user snippets

3. live server

   1. 根据代码修改 实时刷新带页面

4. nodejs

# HTML & CSS

## HTML

1. html
   1. head - head中的东西不会直接显示在页面中
      1. title - 就是个title
      2. meta - 做基础config的地方
      3. style - 写css的地方
      4. link - import css文件
         * href： 文件路径
   2. body
      1. h1 - heading
         * 还有h2- h6，越来越小
      2. p - paragraph
         * class - 设置样式和定位
         * id - 设置样式和定位，必须unique
      3. a - anchor 超链接
         * href - 要跳转的链接
      4. img - image
         * src - 图片的链接
      5. input
         * type：text，password, email ...
         * placeholder

## CSS

* features

  * background-color
  * font-family
  * font-size
  * color
  * border
  * box-sizing
  * box model
    * margin
    * padding
    * width
    * height
    * border

* selector

  * tag

    `<tagName> {...}`

  * class

    `.<className> {...}`

  * id

    `#<Id> {...}`

  * all

    `* {...}`



# DOM Manipulation

* Document Object Model
  * structures representation of html document
  * allows JavaScript to access html elements and styles to manipulate them

* `document.querySelector('<param>')`
  * `document.querySelectorAll('<param>')`
    * 会返还所有匹配的element
  * `getElementById`
    * 用这个就不需要在开头写#了
  * param和css的selector一样
  * textContent: 显示的内容
  * value: 存储的val
  * style
    * feature 都是camel style
      * background-color -> backgroundColor
  * classList
    * 所有class的list
    * 可以想list一样操作，比如`remove("<className>")`
      * `toggle('<className>')` 没有就加上 有就删掉
  * `.addEventListener('<type>', <callbackFunc>)`
    * type can be:
      1. click
      2. keydown
    * callback function有入参
      * event：包含所有需要的信息
* emoji `windows + .`
* refactor code
  * [Dry principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) (**Don't repeat yourself**)
* modal window
  * 弹窗

# How JS works

## variable environment

## scope

1. global scope

   * 对let const 和 var都生效

2. function scope

   * 对let const 和 var都生效

3. block scope (ES6)

   * if loop

   * 这个scope仅对let 和 const生效
     * var声明的变量在scope外仍然生效

* scope chain
  * inner scope can access the outer scope
  * the order of call does not affect the scope chain

## Hosting [变量提升](https://juejin.cn/post/7007224479218663455)

* 在执行代码前，js会进行预编译。这个阶段js收集了所有的variable name并declare。

  * 这样可以有效提升js脚本的运行速度。

  * var和function关键词是早期js语言的产物，所以存在比较特殊的问题：可以先使用 后定义
    * 变量 var
    
      ```js
      num = 6;
      console.log(num); // returns 6
      var num;
      --------------------------------------------
      console.log(num); // returns undefined
      var num = 6;
      ```
    
    * 方法 function
      ```javascript
      testFunc(); // return 2
      function testFunc() {
          console.log(2);
      }
      ```
    
    * 并且var和function声明的变量和方法**xxx**可以通过**window.xxx**的方式获取到

  * 通过**let**和**const**变量声明的变量和方法不可以进行上述操作

    * 因为let和const从设计上避免了一些反直觉的编程习惯(ES6)

      ```js
      num = 6;
      console.log(num); // Uncaught ReferenceError: Cannot access 'num' before initialization
      var num;
      --------------------------------------------
      console.log(num); // Uncaught ReferenceError: Cannot access 'num' before initialization
      let num = 6;
      ```

      * 增加一个在hoisting时的状态TDZ （temporal dead zone）
        * 用于检查代码是否执行到变量已经声明的位置。

    * 这也是为什么**推荐let**而不是var的原因

## this keyword

* 如果method属于某个object时，this指代这个object

  ```js
  const obj = 
  {
      name: "test",
      testFunc: function() {
          console.log(this.name);
      }
  }
  obj.testFunc(); // return "test"
  ```

* 如果是正常声明的method，那么this没有任何指代，就是undefined

  ```js
  function testFunc () {
    console.log(this);
  }
  testFunc(); // return undefined
  ```

* arrow method的this是定义这个method时的scope中的this，比如在global scope中，arrow method的this会获取到window。

  * object declaration并不是一个scope，所以obj中的arrow method的this还是window
    * scope recap：global，function and block（ES6）

* 在事件Event Hanlder中，this 表示接收事件的元素。

* 可以自行绑定object （感觉没啥用）

  ```js
  let person1 = {
    firstName: "Test",
    fullName: function() {
      return this.firstName + " " + this.lastName;
    }
  }
  let person2 = {
    firstName:"John",
    lastName: "Doe",
  }
  console.log(person1.fullName.call(person2));  // return "John Doe"
  ```

## primitives VS objects (reference types)

* primitives 
  * number string boolean undefined null symbol bigint
  * 直接存在call stack中
  * variable指向的是具体的值
* obejcts
  * array object set map ....
  * 存在heap中
  * variable指向的是一个heap中的地址
  * clone一个object
    * 浅拷贝：`let newObj = Object.assign({}, <preObj>)`
    * 深拷贝： // todo

# Data structure & modern operators and strings

## De-structuring Array

* basic

  ```js
  const arr = [1,2,3];
  // destructure
  const [a,b,c] = arr;
  // 等价于
  const a = arr[0];
  const b = arr[1];
  const c = arr[2];
  ```

* 也不用必须全取出来

  ```js
  let arr = [1,2,3];
  // 可以只取一部分
  const [a,b] = arr; // a = 1, b = 2
  // 可以跳着取
  const [a, , c] = arr; // a = 1, c = 3
  ```

* 可以用于swap
  ````js
  [arr[0], arr[1]] = [arr[1], arr[0]]
  ````

* nested de-structuring

  ```js
  const nested = [2,4,[5,6]]
  const [i, , [j, k]] = nested; // i = 2, j = 5, k = 6
  ```

* default values

  ```js
  const [p,q,r] = [8, 9] // p = 8, q = 9, r = undefined
  const [p=1, q=1, r=1] = [8, 9] // p = 8, q = 9, r = 1
  ```

## De-structuring Object

* basic

  ```js
  const obj = {
      name: "Mike",
      age: 24,
      gender: "male"
  }
  const {name, gender} = obj; // name = "Mike", gender = "male"
  // 可以换名字
  const {name: newName, gender: newGender} = obj; // newName = "Mike", newGender = "male"
  ```

* default value

  ```js
  const {job="None"} = obj; // job = "None"
  ```

* mutating variables

  ```js
  let a = 111;
  let b = 999;
  cosnt obj = {a:23, b:7, c:14};
  // 不是声明 仅为赋值时，需要加括号，要不然会被识别为block
  ({a, b} = obj); // a = 23, b = 7
  ```

* nested de-structuring

  ```js
  let obj = {a:{b:2}, c:3};
  let {a: {b}} // b = 2;
  ```

* function parameters de-structuring

  ```js
  // 好处是可以不用考虑顺序问题 （也可以设置default value）
  function testFunc({name, time, age}) {
      console.log(`${name} ${time} ${age}`);
  }
  const obj = {
      name: "Mike",
      age: 24,
      time: "10:00 am"
  }
  
  testFunc(obj) // return "Mike 10:00 am 24"
  ```

## The spread operator (ES6)

* basic

  ```js
  const arr = [7,8,9];
  const newArr = [1, 2, ...arr]; // newArr = [1, 2, 7, 8, 9]
  // 等价于
  const newArr = [1, 2, arr[0], arr[1], arr[2]]
  ```

* shallow copy

  ```js
  // array
  const arr = [7,8,9];
  const copyArr = [...arr];
  
  // object
  const obj = {
      name: "Mike",
      age: 27
  }
  const copyObj = {...obj}
  ```

* join two arrays

  ```js
  const arr1 = [7,8,9];
  const ar2 = [10, 11, 12];
  const joinedArr = [...arr1, ...arr2]
  ```

* 拆String为Char array

  ```js
  const name = "Mike";
  const charName = [...name]; // charName = ['M', 'i', 'k', 'e']
  ```

## REST pattern and parameters

* REST basic （...用在等号左边就是REST）

  ```js
  // array
  const [a, b, ...others] = [1,2,3,4,5]; // a = 1, b = 2, others = [3,4,5]
  // object
  const obj = {
      name: "Mike",
      age: 24,
      time: "10:00 am"
  }
  const {name, ...others} = obj; // name = "Mike", others = {age:24, time: "10:00 am"}
  ```

* function parameter

  ```js
  const add = function(...numbers) {
      console.log(numbers)
  }
  
  add(1,2,3,4,5,6) // return [1,2,3]
  ```

  



# 其他topic

1. prototypal inheritance
2. event loop
3. how the dom really works

# 一些发现

* let in & let of
  * let in是随机顺序把目标对象中的成员都遍历一遍
    * <img src="JS/image-20230421151839723.png" alt="image-20230421151839723" style="zoom: 67%;" />
  * let of 是正常的遍历目标对象中存储的内容 (**这个是常用的那个**)  

* [map和{}的区别](https://blog.csdn.net/muzidigbig/article/details/121995777#:~:text=Map%20%E6%98%AF%E7%BA%AF%E7%B2%B9%E7%9A%84hash,key%20%E7%9A%84%E7%B1%BB%E5%9E%8B%E7%9A%84%E6%83%85%E5%86%B5%E3%80%82)
  * map是es6的产物，更接近其他语言中的map
  * object 删除的效率不如map
  * object 只能把比较基础的类型作为key，但是map没有限制