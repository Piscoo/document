## bind原理、使用场景及模拟实现

## bind()

> `bind()`方法会创建一个新函数，当这个新函数被调用时，它的`this`值是传递给`bind()`的第一个参数，传入`bind`方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。`bind`返回的绑定函数也能使用`new`操作符创建对象：这种行为就像把原函数当成构造器，提供的`this`值被忽略，同时调用时的参数被提供给模拟函数

语法：`fun.bind(thisArg[, arg1[, arg2[, ...]]])`

`bind`方法与`call/apply`最大的不同就是前者返回一个绑定上下文的**函数**，而后两者是**直接执行**了函数。

举例：

```
var value = 2;

var foo = {
    value: 1
};

function bar(name, age) {
    return {
        value: this.value,
        name: name,
        age: age
    }
};

bar.call(foo, "Pisco", 22); // 直接执行了函数
// {value: 1, name: "Pisco", age: 22}

var bindFoo1 = bar.bind(foo, "Pisco", 22); // 返回一个函数
bindFoo1();  // {value: 1, name: "Pisco", age: 22}

var bindFoo2 = bar.bind(foo, "Pisco"); // 返回一个函数
bindFoo2(22); // {value: 1, name: "Pisco", age: 22}
```

通过上述代码可以看出 `bind` 有如下特性：

1. 可以指定 `this`
2. 返回一个函数
3. 可以传入参数
4. 柯里化


## 使用场景

### 1. 业务场景

经常有如下的业务场景

```
var nickname = "Pisco";

function Person(name) {
    this.nickname = name;
    this.distractedGreeting = function() {
        setTimeout(function() {
            console.log(this.nickname);
        }, 1000);
    }
}

var person = new Person('test');
person.distractedGreeting(); // Pisco
```

这里输出的`nickname`是全局的，并不是我们创建`person`时传入的参数，因为`setTimeout`在全局环境中执行，所以`this`指向的时`window`。

这边把`setTimeout`换成异步回调也是一样的，比如接口请求回调。

解决方案有以下两种：

**解决方案一**  缓存`this`值
```
var nickname = "Pisco";

function Person(name) {
    this.nickname = name;
    this.distractedGreeting = function() {
        var self = this;
        setTimeout(function() {
            console.log(self.nickname);
        }, 1000);
    }
}
var person = new Person("test");
person.distractedGreeting();  // test
```

**解决方案二：**使用`bind`
```
var nickname = "Pisco";
function Person(name) {
    this.nickname = name;
    this.distractedGreeting = function() {
        setTimeout(function() {
            console.log(this.nickname);
        }.bind(this), 1000);
    }
}

var person = new Person("test");
person.distractedGreeting(); // test
```

### 2. 验证是否是数组

先看一下`call`的使用场景
```
function isArray(obj) {
    return Object.prototype.toString.call(obj) === '[object Array]';
}
isArray([1, 2, 3]); // true

// 直接使用 toString()
[1, 2, 3].toString(); // "1,2,3"
"123".toString(); // "123"
123.toString(); // SyntaxError: Invalid or unexpected token
Number(123).toString(); // "123"
Object(123).toString()
```

可以通过`toString()`来获取每个对象的类型，但是不同对象的`toString()`有不同的实现，所以通过`Object.prototype.toString()`来检测，需要以`call()/apply()`的形式来调用，传递要检查的对象作为第一个参数。

另一个**验证是否是数组**的方法，这个方案的**优点**是可以直接使用改造后的`toStr`。

```
var toStr = Function.prototype.call.bind(Object.prototype.toString);
function isArray(obj) {
    return toStr(obj) === '[object Array]';
}
isArray([1, 2, 3]);  // true

// 使用改造后的 toStr
toStr([1, 2, 3]); // "[object Array]"
toStr("123");  // "[object String]"
toStr(123); // "object Number"
toStr(Object(123)); // "[object Number]"
```

上面方法首先使用`Function.prototype.call`函数指定一个`this`值，然后`.bind`返回一个新的函数，始终将`Object.prototype.toString`设置为传入参数。其实等价于`Object.prototype.toString.call()`。

这里有一个**前提**是`toString()`方法**没有被覆盖**

```
Object.prototype.toString = function() {
    return '';
}
isArray([1, 2, 3]);  // false
```

### 3. 柯里化

> 只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

可以一次性地调用柯里化函数，也可以每次只传一个参数分多次调用。
```
var add = function(x) {
    return function(y) {
        return x + y;
    };
};

var increment = add(1);
var addTen = add(10);

increment(2); // 3
addTen(2);  // 12
add(1)(2);  // 3
```

这里定义了一个`add`函数，它接受一个参数并返回一个新的函数。调用`add`之后，返回的函数就通过闭包的方式记住了`add`的第一个参数。所以说`add`本身也是闭包的一种使用场景。

### 模拟实现

`bind()`函数在ES5才被加入，所以并不是所有浏览器都支持，`IE8`及以下版本中不被支持，如果需要兼容可以使用`Polyfill`来实现。

首先我们实现以下四个特性：
1. 可以指定`this`
2. 返回一个函数
3. 可以传入参数
4. 柯里化

### 模拟实现第一步

对于第一点，使用`call/apply`指定`this`。
对于第二点，使用`return`返回一个函数。
```
// 第一版
Function.prototype.bind2 = function(context) {
    var self = this; // this 指向调用者
    return function() {   // 实现第二点
        return self.apply(context); // 实现第一点
    }
}
// test
var value = 2;
var foo = {
    value: 1
};
function bar() {
    return this.value;
}
var bindFoo = bar.bind2(foo);
bindFoo(); // 1
```

### 模拟实现第二步

对于第三点，使用`arguments`获取参数数组并作为`self.apply()`的第二个参数。

对于第四点，获取返回函数的参数，然后同第三点的参数合并成一个参数数组，并作为`self.apply()`的第二个参数。

```
// 第二版
Function.prototype.bind2 = function(context) {
    var self = this;
    // 实现第三点，因为第一个参数是指定的this，所以只截取第一个之后的参数
    // arr.slice(begin); 即[begin, end]
    var args = Array.prototype.slice.call(arguments, 1);

    return function() {
        // 实现第四点，这时的arguments是指bind返回的函数传入的参数
        // 即 return function的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(context, args.concat(bindArgs));
    }
}
// test
var value = 2;
var foo = {
    value: 1;
};
function bar(name, age) {
    return {
        value: this.value,
        name: name,
        age: age
    }
};
var bindFoo = bar.bind2(foo, "Pisco");
bindFoo(22); // {value: 1, name: "Pisco", age: 22}
```

### 模拟实现第三步

到目前还剩下一个难点，`bind`有以下一个特性
> 一个绑定函数也能使用`new`操作符创建对象：这种行为就像把原函数当成构造器，提供的`this`值被忽略，同时调用时的参数被提供给模拟函数。

例如：
```
var value = 2;
var foo = {
    value: 1
};
function bar(name, age) {
    this.habit = "shopping";
    console.log(this.value);
    console.log(name);
    console.log(age);
}
bar.prototype.friend = "Pisco";
var bindFoo = bar.bind(foo, "Suki");
var obj = new bindFoo(22);
// undefined
// Suki
// 22


obj.habit;  // shopping

obj.friend; // Pisco
```

上面例子中，运行结果`this.value`输出为`undefined`，这不是全局`value`也不是`foo`对象中的`value`，这说明`bind`的`this`对象失效了，`new`的实现中生成了一个新的对象，这个时候的`this`指向的是`obj`。

这里可以通过修改返回函数的原型来实现

```
// 第三版
Function.prototype.bind2 = function(context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fBound = function() {
        var bindArgs = Array.prototype.slice.call(arguments);

        // 注释1
        return self.apply(
            this instanceof fBound ? this : context,
            args.concat(bindArgs)
        );
    }
    // 注释2
    fBound.prototype = this.prototype;
    return fBound;
}
```
+ 注释1 
   - 当作为构造函数时，`this`指向实例，此时`this instanceof fBound` 结果为`true`，可以让实例获得来自绑定函数的值，即上例中实例会具有`habit`属性。
   - 当作为普通函数时，`this`指向`windwo`，此时结果为`false`，将绑定函数的`this`指向`context`

+ 注释2： 修改返回函数的`prototype`为绑定函数的`prototype`，实例就可以继承绑定函数的原型中的值，即上例中`obj`可以获取到`bar`原型上的`friend`。

### 模拟实现第四步

上面实现`fBound.prototype = this.prototype`有一个缺点，直接修改`fBound.prototype`的时候，也会直接修改`this.prototype`。

解决方案是用一个空对象作为中介，把`fBound.prototype`赋值为空对象的实例(原型式继承)。

```
var fNOP = function() {}; // 创建一个空对象
fNOP.prototype = this.prototype;  // 空对象的原型指向绑定函数的原型
fBound.prototype = new fNOP(); // 空对象的实例赋值给 fBound.prototype
```
也可以直接使用ES5的`Object.create()`方法生成一个新对象
```
fBound.prototype = Object.create(this.prototype);
```
不过`bind`和`Object.create()`都是ES5方法，部分IE浏览器不支持，Polyfill中不能用`Object.create()`实现`bind`，不过原理一样。

```
// 第四版
Function.prototype.bind2 = function(context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fNOP = function() {};
    var fBound = function() {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(
            this.instanceof fNOP ? this : context,
            args.concat(bindArgs)
        );
    }
    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    return fBound;
}
```

### 模拟实现第五步

到这里已经差不多了，有一个问题是调用`bind`的不是函数时，需要抛出异常

所以完整版模拟代码如下：

```
// 第五版
Function.prototype.bind2 = function(context) {
    if(typeof this !== "function") {
        throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fNOP = function() {};
    var fBound = function() {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(
            this.instanceof fNOP ? this : context,
            args.concat(bindArgs)
        );
    }
    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    return fBound;
}
```