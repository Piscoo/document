## JS数组知识
#### 数组常用方法
+ 添加/删除元
   - push(...items) --- 从结尾添加元素
   - pop() --- 从结尾提取元素
   - shift() --- 从开头提取元素
   - unshift(...items) --- 从开头添加元素
   - splice(pos, deleteCount, ...items) --- 从pos开始，删除deleteCount个元素并在当前位置插入元素。
   - slice(start, end) --- 从索引为start的位置复制到end（不包括end）返回一个新数组
   - concat(...items) --- 返回一个新数组：复制当前数组所有元素并添加items，如果任何items是一个数组，那就取其元素
   - indexOf/lastIndexOf(item, pos) --- 从pos找到item，则返回索引，否则返回-1
   - includes(value) --- 如果数组有value，则返回true，否则返回false
   - find/filter(func) --- 通过函数过滤元素，返回true条件的符合find函数的第一个值或者符合filter函数的所有值
   - findIndex ---和find类似，区别是返回索引而不是值

+ 转换数组
   - map(func) --- 从每个元素调用func的结果创建一个新的数组
   - sort(func) --- 将数组倒序排列，然后返回
   - reverse() --- 原地颠倒数组，然后返回
   - split/join --- 将字符串转换为数组并返回
   - reduce(func, initial) --- 通过为每个元素调用func函数计算数组上的单个值并在调用之间传递中间结果

+ 迭代元素
   - forEach(func) --- 为每个元素调用func，不返回
+ 其他   Array.isArray(arr) --- 检查arr是否是一个数组

- 注意：sort，reverse和splice方法修改数组本身。
- 这些是最常用的数组方法，还有几个其他的
   * arr.some(fn)/arr.every(fn)检查数组。--- 类似于map，在数组每个元素上调用fn，如果任何/所有结果为true，则返回true，否则返回false
   * arr.fill(value, start, end) --- 从start到end用value填充数组
   * arr.copyWithin(target, start, end) --- 将其元素从start到end在target位置复制到本身（覆盖现有）

1. 生成类似[1-100]这样的数组
```
let arr = new Array(100).fill(0).map((item, index) => index + 1);
```

2. 数组解构赋值应用
```
// 交换变量
[a, b] = [b, a]
[o.a, o.b] =[o.b, o.a]
// 生成剩余数组
const [a, ...rest] = [...'asdf'] // a: 'a', rest: ['s', 'd', 'f']
```

3. 数组浅拷贝
```
const arr = [1, 2, 3];
const arrClone = [...arr];
// 对象也可以这样浅拷贝
const obj = { a: 1 };
const objClone = { ...obj };
// 浅拷贝的方法有很多，如arr.slice(0, arr.length)/Array.from(arr)等。但是...操作符更好用一点
```

4. 数组合并
```
const arr1 = [1, 2, 3, 4];
const arr2 = [5, 6];
const arr3 = [7, 8];
const arr = [...arr1, ...arr2, ...arr3]
// arr1.concat(arr2, arr3)也可以，但是...操作符更好用一点
```

5. 数组去重
```
const arr = [1, 1, 2, 3, 4, 5, 3, 5];
const newArr = [...new Set(arr)]
// new Set(arr)接受一个数组参数并生成一个set结构的数据类型。set数据类型的元素不会重复且是Array Iterator，所以可以利用这个特性来实现去重。
```

6. 数组取交集
```
const a = [0, 1, 2, 3, 4, 5];
const b = [3, 4, 5, 6, 7, 8];
const duplicatedValues = [...new Set(a)].filter(item => b.includes(item));
// duplicatedValues ==> [3, 4, 5]
```

7. 数组取差集
```
const a = [0, 1, 2, 3, 4, 5];
const b = [3, 4, 5, 6, 7, 8];
const diffValues = [...new Set([...a, ...b])].filter(item => !a.includes(item) || !b.includes(item));
// diffValues ==> [0, 1, 2, 6, 7, 8]
```

8. 数组转对象
```
const arr = [1, 2, 3, 4];
const newObj = { ...arr }; // { 0: 1, 1: 2, 2: 3, 3: 4 }
const obj = { 0: 0, 1: 1, 2: 2, length: 3};
// 对象转数组不能用展开操作符，因为展开操作符必须用在可迭代对象上
let newArr = [...obj];
// Uncaught TypeError: object is not iterable...
// 可以用Array.from()将类数组对象转换为数组
let newArr = Array.from(obj); [0, 1, 2]
```

9. 数组常用遍历
```
数组常用的遍历方法有：forEach, every, some, filter, map, reduce, reduceRight, find, findIndex等方法，很多方法都可以达到同样的效果
```

10. 遍历的混合使用
```
// filter, map方法返回值仍旧是一个数组，所以可以搭配其他数组遍历方法混合使用。注意遍历越多效率越低～
const arr = [1, 2, 3, 4, 5];
const value = arr.map(item => item * 3).filter(item => item % 2 === 0).map(item => item + 1).reduce((prev, curr) => prev + curr, 0)
```

11. 检查数组所有元素是否都符合判断条件
```
const arr = [1, 2, 3, 4, 5];
const isAllNum = arr.every(item => typeof item === "number");
```

12. 检查数组是否有元素符合判断条件
```
const arr = [1, 2, 3, 4, 5];
const isAllNum = arr.some(item => typeof item === "number");
```

13. 找到第一个符合条件的元素/下标
```
const arr = [1, 2, 3, 4, 5];
const findItem = arr.find(item => item === 3);
const findIndex = arr.findIndex(item => item === 3);
```

#### 数组使用误区
> 数组的方法很多，很多方法可以达到同样的效果，所以在使用时要根据具体情况使用合适的方法。

+ array.includes() 和 array.indexOf()
> array.includes()返回布尔值；array.indexOf()返回数组子项的索引。indexOf一定要在需要索引值的情况下使用。
```
const arr = [1, 2, 3, 4, 5];
// 使用indexOf,需要用到索引值
const index = arr.indexOf(1) // 0
if(~index) { // 若index===-1，～index得到0，判断不成立；若index不为-1，则～index得到非0，判断成立
    arr.spilce(index, 1);
}

// 使用includes，不需要用到索引值
// 此时如果使用indexOf会造成上下文的阅读负担：到底别的地方有没有用到这个index
const isExist = arr.includes(6);  // true
if(!isExist) {
    arr.push(6)
}
```

+ array.find()、array.findIndex()、array.some()
> array.find()返回值是第一个符合条件的数组子项；array.findIndex()返回第一个符合条件的数组子项的下标；array.some()根据是否有符合条件的子项返回true/false。这三个都是短路操作，即找到符合条件的之后就不再遍历数组。
> 在需要数组子项的时候使用array.find();需要索引值的时候使用array.findIndex();如果只需要知道有无符合条件的子项，则用array.some()
```
const arr = [{label: "男", value: 0}, {label: "女", value: 1}, {label: "不男不女", value: 2}];
// 使用some
const isExist = arr.some(item => item.value === 2);
if(isExist) {
    console.log("找到了");
}
// 使用find
const item = arr.find(item => item.value === 2);
if(item) {
    console.log(item.label);
}
// 使用findIndex
const index = arr.findIndex(item => item.value === 2);
if(~index) {
    const delItem = arr[index];
    arr.splice(index, 1);
    console.log(`你删除了${delItem.label}`);
}
```
> 建议在只需要布尔值的时候和数组子项是字符串或数字的时候使用array.some()
```
// 当子项包含数字0的时候可能出错
const arr = [0, 1, 2, 3, 4, 5];
// 正确
const isExist = arr.some(item => item === 0);
if(isExist) {
    console.log("exist");
}
// 错误
const isExist = arr.find(item => item === 0);
if(isExist) { // 此时isExist值为0，隐式转换为布尔值是false
    console.log("exist"); // 执行不到这里
}
// 当子项包含空字符串的时候也可能出错，原因同上，“”隐式转换为布尔值为false
```

+ array.find() 和 array.filter()
> 只需知道array.filter()返回的是所有符合条件的子项组成的数组，会遍历数组所有的元素；而array.find()只会返回第一个符合条件的子项，是短路操作

+ 合理使用Set数据结构
> 由于ES6原生提供了Set数据结构，而Set可以保证子项不重复，且和数组转换十分方便，所以在一些地方可能涉及重复添加的场景下可以直接使用Set代替Array，避免判断是否已经存在改子项
```
const set = new Set();
set.add(1);
set.add(1);
set.add(1);
set.size; // 1
const arr = [...set]; // arr: [1]
```

+ 强大的reduce
> array.reduce()遍历并将当前次回调函数的返回值作为下一次回调函数执行的第一个参数。利用array.reduce替代一些需要多次遍历的场景，可以提高代码的运行效率。
```
// 假如有每个元素都由字母's'加数字组成的数组，现在找出其中最大的数字
const arr = ['s0', 's6', 's2', 's8', 's4'];
// 方法1  进行多次遍历
const newArr = arr.map(item => item.substring(1)).map(item => Number(item));
const maxS = Math.max(...newArr);
// 方法2  一次遍历
const maxS = arr.reduce((prev, curr) => {
    const curIndex = Number(curr.replace('s', ''));
    return curIndex > prev ? curIndex : prev;
}, 0)
```
```
const arr = [1, 2, 3, 4, 5, 6];
// 方法1 遍历两次
const value = arr.map(item => item % 2 === 0).map(item => ({ value: item }));
// 方法2 遍历一次
const value = arr.reduce((prev, curr) => {
    return curr % 2 === 0 ? [...prev, curr] : prev;
}, [])
```
> 也可以用reduce做下面这样的处理来生成想要的html结构
```
// 后端返回数据
const data = {
    'if _ then s9': [
        '作用属于各种,结构属于住宅,结构能承受作用,作用属于在正常建造和正常使用过程中可能发生',
        '作用属于各种,结构属于住宅,结构能承受作用,作用属于在正常建造和正常使用过程中可能发生',
        '作用属于各种,结构属于住宅,结构能承受作用,作用属于在正常建造和正常使用过程中可能发生'
    ],
    'if C then s4': [
        '当有条件时时,结构构件满足要求,要求属于安全性、适用性和耐久性',
        '当有条件时时,住宅结构满足要求,要求属于安全性、适用性和耐久性'
    ]
};
const ifthens = Object.entries(data).reduce((prev, curr) => {
    const values = curr[1].reduce((prev, curr) =>  `${prev}<p>${curr}</p>`, '');
    return `
        ${prev}
        <li>
            <p>${curr[0]}</p>
            ${values}
        </li>
    `;
}, '');
const html = `
    <ul>
        ${ifthens}
    </ul>
`;
```
> 生成的html结构
```
<ul class="nlp-notify-body">            
    <li>
        <p>if _ then s9</p>
        <p>作用属于各种,结构属于住宅,结构能承受作用,作用属于在正常建造和正常使用过程中可能发生</p>
        <p>作用属于各种,结构属于住宅,结构能承受作用,作用属于在正常建造和正常使用过程中可能发生</p>
        <p>作用属于各种,结构属于住宅,结构能承受作用,作用属于在正常建造和正常使用过程中可能发生</p>
    </li>
    <li>
        <p>if C then s4</p>
        <p>当有条件时时,结构构件满足要求,要求属于安全性、适用性和耐久性</p>
        <p>当有条件时时,住宅结构满足要求,要求属于安全性、适用性和耐久性</p>
    </li>
</ul>
```