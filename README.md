#### Generator函数
* 对于一个 Generator 函数，需要在函数名之前加星号
* 调用 Generator 函数，会返回一个内部指针。指针 g 的 next 方法，会移动内部指针，指向下一个遇到的 yield 语句
```
function* gen() { 
  yield 1;
  yield 2;
  yield 3;
}

let g = gen(); 
```
```
g.next() // {value: 1, done: false}
g.next() // {value: 1, done: false}
g.next() // {value: 1, done: false}，注意这里的 done 还是 false
g.next() // {value: undefined, done: true}
```

#### next 方法可以接受参数
```
function* gen(x){
  var y = yield x + 2;
  return y;
}

var g = gen(1);
g.next() // { value: 3, done: false }
g.next(2) // { value: 2, done: true }
```
> 上面代码中，第一个 next 方法的 value 属性，返回表达式 x + 2 的值（3）。第二个 next 方法带有参数2，这个参数可以传入 Generator 函数，作为上个阶段异步任务的返回结果，被函数体内的变量 y 接收。因此，这一步的 value 属性，返回的就是2（变量 y 的值）。

#### Generator 函数执行异步任务
```
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}
```
```
var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```
上面代码中，首先执行 Generator 函数，获取遍历器对象，然后使用 next 方法（第二行），执行异步任务的第一阶段。由于 Fetch 模块返回的是一个 Promise 对象，因此要用 then 方法调用下一个next 方法。
