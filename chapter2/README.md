# 字面量和构造函数

## 对象构造函数捕捉
Object()构造函数仅接收一个参数，并且依赖传入的值，该Object()可能会委派另一个内置构造函数来创建对象，并返回一个并非期待的不同对象。
```js
var o = new Object(1);
o.constructor === Number;//true
//该对象的构造函数是Number
```

# 函数

## 函数属性——备忘模式
任何时候可以在函数上添加自定义属性，特别是来缓存函数结果。例如：
```js
var Fn=function(param){
  if(!Fn.cache[param]){
    var result={};
    //某种大开销操作
    Fn.cache[param]=result;
  }
  return Fn.cache[param];
}
```

## 配置对象
将函数的多个参数转化为一个对象参数。例如：
```js
function (a,b,c)...
//改写
function ({
  name : a,
  age : b,
  hobby : c
})...
```
优点：
+ 不需要记住参数及其顺序
+ 可以安全的忽略可选参数
+ 更加易于阅读和维护
+ 更加易于添加和删除
缺点：
+ 需要记住参数名称
+ 属性名称无法压缩