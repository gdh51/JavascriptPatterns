# 设计模式

## 单例模式(Singleton)
保持特定类只有一个实例对象，在次创建时返回第一个实例。
### 利用构造函数属性保存实例
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

### 利用闭包保存实例
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

### 利用原型对象保存实例
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

## 工厂模式(Factory)
区分于其他模式的地方在于不显式的要求使用一个构造函数。它提供一个通用的接口来指定希望创建的工厂对象的类型。

```js
function Factory(){};

Factory.prototype.instanceMethod = function(){};//定义实例方法
Factory.staticMethod = function(){};//定义静态方法
```

### 何时使用
+ 当对象或组件设置设计高复杂性时
+ 当需要根据不同环境轻松生成对象的不同实例时
+ 当处理很多共享的相同属性的小型对象或组件时

### Abstract Factory(抽象工厂)
用于封装一组具有共同目标的单个工厂


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

## 装饰者模式(Decorator)
可以在运行时动态添加附加功能到对象中；预期行为的可定制和可配置————从一个普通对象开始，然后从可用的资源池选择需要用于增强这个对象的功能。

下面代码通过创建新的继承于Sale的实例来拥有新的功能
```js
function Sale(price) {
  this.price = pirce || 100;
}
Sale.prototype.getPrice = function (k) {
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

## 外观模式(Facade)
为代码提供一个更高层次的接口，能够隐藏其底层的真实复杂性。

`Facade`是一种结构型,著名的JQuery就是`Facade`模式。

```js
//将内部底层API抽象为更高级的API
let module = (function (){
  let _private = {
    i: 5,
    get(){
      console.log(this.i);
    },

    set(val){
      this.i = val;
    }
  }

  return {
    facade(args){
      _private.set(args.val);
      _private.get();
    }
  }
})();
```

## 代理模式
代理介于对象的客户端和对象本身之间，对对象的访问进行保护。

## 观察者模式(Observer)
当一个被观察目标发生变化后会自动向观察者广播一个通知。

代码实现：
```js
    //观察者队列及其自身的操作方法
    class ObserverList {
      constructor(){
        this.list = [];
      }
      //队列长度
      get length(){
        return this.list.length;
      }

      //队列操作逻辑
      add(ob){
        return this.list.push(ob);
      }

      del(ob){
        let index = this.list.indexOf(ob);
        return this.list.splice(index, 1);
      }

      get(index){
        return this.list[index];
      }

      empty(){
        return this.list = [];
      }
    }

    //单个观察者对象
    class Observer {
      constructor(callback){//传入回调函数在目标发生变换后调用
        this.update = callback;
      }
    }

    //被观察对象的监听器
    class Subject {
      constructor(){
        this.observers = new ObserverList();
      }

      //目标发生改变时，通知全部观察者(在什么时候通知,需要自主添加逻辑)
      //(如，我需要在点击事件的回调时触发)
      notify(){
        for(let i = 0; i < this.observers.length; i++){
          this.observers.get(i).update();
        }
      }

      addObserver(ob){
        this.observers.add(ob);
      }

      removeObserver(ob){
        this.observers.del(ob);
      }
    }
```

## 发布/订阅模式(Pubilish/Subscribe)
该模式和观察者模式较为相识，使用了一个主题/事件通道,这个通道介于订阅者和发布者之间, 其目的是**避免两者产生依赖关系**。

```js
//一个简单的代码实现
    class SubscribePipe {
      constructor(){
        this.list = {};
      }

      publish(name, ...args){
        this.list[name].apply(null, args);
      }

      subscribe(name, callback){
        this.list[name] = callback;
      }

      unsubscribe(name){
        delete this.list[name];
      }
    }


    let eventList = new SubscribePipe();
    eventList.subscribe('al', function (word) {
      console.log(word);
    });
    eventList.publish('al', 'Hi');
```

## 中介者模式(Mediator)
独立的对象之间并不直接通信，它允许公开一个接口，系统的不同部分可以通过该接口进行通信。其本质上是`Observer`模式的共享目标。
(这个模式和订阅发布模式及其相似，不同之处在于该模式返回一个接口对象来暴露发布订阅逻辑)

优点：解耦程度高
缺点：引入单一故障点; 模块间接通信,性能降低。


## 原型模式(Prototype)
即使用对象的原型属性的模式

## 命令模式(Command)
旨在将方法调用、请求或操作封装到单一对象中,从而根据我们不同的请求对客户进行参数化和传递可执行的方法调用。

这种方法的主要思想是将代码职责分离，这些职责包括执行命令的任意地方发布命令以及将职责转而委托给不同对象

如：
```js
(function (){
  let carManage = {
    requestInfo(model, id){
      return model + id;
    },

    buyVehicle(model, id){
      return model + id;
    },

    arrangeViewing(model, id){
      return model + id;
    }
  }
})();
//以上方法的调用在Command模式下可以改写为

//这里只写方法
carManage.execute = function(name){
  return carManage[name] && carManage[name].apply(carManage, [].slice.call(arguments, 1));
}

//然后通过单独的一个方法来调用所有的方法
carManage.execute('requestInfo', model, id);
```

## Mixin模式
允许对象通过较低的复杂性借用(或继承)功能。

通过call(),apply()来继承Mixin构造函数的属性

## 享元模式(Flyweight)
结构型解决方案，用于优化重复、缓慢及数据共享效率较低的代码。它旨在通过相关对象共享尽可能多的数据来减少应用程序内存的使用

Flyweight共有两种应用：
1. 用于数据层，处理内存中保存的大量相似对象的共享数据
2. 用于DOM层,当作中央事件管理器,来避免将事件处理器附加到父容器的各个子元素上,而是将事件处理程序附加到这个父容器上。

附上一个书中例子，实现一个图书管理系统。

图书除了本身的信息外，还有借还信息,当书籍量少时，可以通过一个对象管理，但当数据量大时，就会比较麻烦。

利用享元模式将数据分为内部数据(书籍信息)和外部数据(书籍借出信息)
```js
const BookFactory = (function () {
  let existingBooks = {}, existingBook;

  //内部信息
  const Book = function (title, author, pageCount, ISBN){
    this.title = title;
    this.author = author;
    this.pageCount = pageCount;
    this.ISBN = ISBN;
  }

  return {
    createBook: function (title, author, pageCount, ISBN) {
      existingBook = existingBooks[ISBN];

      if(!!existingBook){
        return existingBook;
      } else {
        let book = new Book(title, author, pageCount, ISBN);
        existingBooks[ISBN] = book;
        return book;
      }
    }
  }
})();

const BookRecordManager = (function () {
  let BookRecordDatabase = {};

  return {
    addBookRecord(id, title, author, pageCount, ISBN, checkOutDate,
    checkOutNumber, returnDate, availability){
      let book = BookFactory.createBook(title, author, pageCount, ISBN);

      BookRecordDatabase[id] = {
        book,
        checkOutDate,
        checkOutNumber,
        availability,
        returnDate
      }
    },

    updateCheckoutStatus(id, checkOutDate, checkOutNumber, returnDate, availability){
      let record = BookRecordDatabase[id];
      record.checkOutDate = checkOutDate;
      record.checkOutNumber = checkOutNumber;
      //......
    }
  }
})();
```