# 设计模式

## 单例模式
保持特定类只有一个实例对象，在次创建时返回第一个实例。
*利用构造函数属性保存实例*
```js
function Singleton() {
  if(Singleton.instance){
    return Singleton.instance;
  }
  this.a = 1;
  this.b = 2;
  Singleton.instance = this;
}
```
缺点：该属性可以被修改

*利用闭包保存实例*
```js
//第一种
function Singleton() {
  var instance = this;
  this.a = 1;
  this.b = 2;
  Singleton = function () {
    return instance;
  }
}
//第二种
var Singleton;
(function (){
  var instance;

  Singleton = function (){
    if(instance){
      return instance;
    }

    instance = this;

    this.a = 1;
    this.b =2;
  }
})();
```
缺点：会在重新定义时失去添加到里面的属性

*利用原型对象保存实例*
```js
function Singleton() {
  var instance;//缓存实例
  //重写构造函数
  Singleton = function () {
    return instance;
  }
  //保存原型属性
  Singleton.prototype = this;

  instance = new Singleton();
  instance.constructor = Singleton;
  //定义功能
  this.a = 1;
  this.b = 2;

  return instance;
  }
```

## 工厂模式
目的：创建相似对象时执行重复的操作；在编译不知道具体类时，为工厂客户提供一种创建对象的接口。
```js
function Factory(){};

Factory.prototype.instanceMethod = function(){};//定义实例方法
Factory.staticMethod = function(){};//定义静态方法
```

### 内置对象工厂
全局的Object()构造函数页表现出工厂行为，当参数为不同的原始值时，调用的构造函数不同。

## 迭代器模式
通常包含某种数据结构集合的对象，然后在提供一个简单的方法来访问数据结构的每个元素(通常为next()方法)。
```js
var Iterator = (function (){
  var index = 0;
  data = [1,2,3,4,5];
  length = data.length;

  return {
    next: function (){
      if(!this.hasNext()) {
        return null;
      }

      element = data[index];
      index = index + 2;
      return element;
    },

    hasNext: function (){
      return index < length;
    }
  }
})();
```

## 装饰者模式
可以在运行时动态添加附加功能到对象中；预期行为的可定制和可配置————从一个普通对象开始，然后从可用的资源池选择需要用于增强这个对象的功能。

下面代码通过创建新的继承于Sale的实例来拥有新的功能
```js
function Sale(price) {
  this.price = pirce || 100;
}
Sale.prototype.getPrice = function () {
  return this.price;
}

Sale.prototype.decorate = function (decorator) {
  var F = function () {},
  overrides = this.constructor.decorators[decorator],
  i,newobj;

  F.prototype = this;
  newobj = new F();
  newobj.uber = F.prototype;//用uber存储父对象
  for (i in overrides){
    if (overrides.hasOwnProperty(i)){
      newobj[i] = overrides[i];//将所有装饰属性定义在装饰对象上
    }
  }
  return newobj;//返回该装饰对象
}

Sale.decorators = {};
Sale.decorators.fedtax = {
  getPrice: function () {
    var price = this.uber.getPrice();//从父对象的方法取得price值
    price += price * 5 / 100;
    return price;
  }
}
```

*通过不断的传递结果来作为参数到下一个方法(使用列表实现)*
```js
function Sale(price){
  this.price = (price > 0) || 100;
  this.decorators_list = [];
}

Sale.decorators = {};
Sale.decorators.fedtax = {
  getPrice: function (price){
    return price + price * 5 / 100;
  }
};
Sale.decorators.quebec = {
  getPrice: function(price){
    return price + price * 7.5 / 100;
  }
}
Sale.protoype.decorate = function (decorator){
  this.decorators_list.push(decorator);
};

Sale.prototype.getPirce = function(){
  var price =this.price,
  i,
  max = this.decorators_list.length,
  name;
  for (i = 0; i < max; i++){
    name = this.decorators_list[i];
    price = Sale.decorators[name].getPirce(price);
  }
  return price;
}
```

## 策略模式
支持运行时选择算法。

## 外观模式
当一些方法被一起调用多次时，可以把它们封装到一个函数中。

## 代理模式
代理介于对象的客户端和对象本身之间，对对象的访问进行保护。

## 中介者模式
独立的对象之间并不直接通信，通过中介者对象进行通信。

## 观察者模式
一个对象订阅另一个对象的特定活动并在状态改变后获得通知。