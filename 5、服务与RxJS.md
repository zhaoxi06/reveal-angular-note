在Angular中，服务用于书写可重用的公共功能（如日志处理、权限管理等）和复杂业务逻辑，对于应用程序的模块化有着很重要的意义。Angular服务一般是封装了特地功能的独立模块，它可以通过注入的方式供外部调用，服务在Angular中使用十分广泛，例如：
* 多个组件中出现重复代码时，把重复代码提取到服务中实现代码重用
* 当组件中掺杂了大量的业务代码和数据处理逻辑时，把这些逻辑封装成服务供组件使用，组件只负责UI相关的逻辑，有利于后续的更新和维护。
* 把需要共享的数据存储在服务中，通过在多个组件中注入同一个服务实例实现数据共享。
以下是Angular应用中常见的几种服务：
* 和服务器通讯的数据服务
* 检查用户输入的验证服务
* 方便跟踪错误的日志服务

## 响应式编程
响应式编程即一种面向数据流和变化传播的编程范式。面向变化传播就是看最初的数据是否会随着后续对应变量的变化而变化。面向数据流就是当监听一系列事件流并对这一系列事件流进行映射、过滤和合并等处理后，再响应整个事件流的回调。
### ReactiveX
ReaxtiveX的编程范式中，数据流被封装在一个叫作Observable的对象实例中，通过观察者模式，对数据流进行统一的订阅(subscribe)，并在中间插入像filter()这样的操作函数，从而对Observable所封装的数据流进行过滤处理。
#### Observable
在RX中，应用中产生的异步数据都需要先包装成Observable对象，Observable对象的作用是把这些异步的数据变换为数据流形式。生成的这些Observable对象相当于数据流的源头，后续的操作都是围绕着这些被转换的流动数据展开。
#### Operator
Operator是Rx中Observable的操作符。在Rx中，每一个Observable对象，或者说数据流，都可以通过某个operator对该Observable对象进行变换、过滤、合并和监听等操作。同时，大多数的operator在对Observable对象处理后返回一个新的Observable对象提供下一个operator进行处理。这样方便在各个operator之间通过链式调用的方式编写代码。
在Rx中，Observable作为观察者模式中的被观察者，需要一个方法来订阅它，而subscribe()便是这样的一种方法，订阅Observable对象发出的所有事件。
subscribe()方法会接收一个observer作为参数，来对observable发出的事件进行订阅。每当observable完成并发送（Emit）一个事件时，该事件就会被observer所捕获，进入到observer对应的回调函数中。被subscribe()订阅过的Observable对象并不会返回一个新的Observable对象，因为subscribe()不是一个可以改变原始数据流的函数。相反，subscribe()会返回一个Subscription实例，这个实例又提供很多操作api，例如具有取消订阅事件功能的unsubscribe()。
### 其他核心概念
* Observer：对Observable对象发出的每个事件进行响应
* Subscription：Observable对象被订阅后返回的Subscription实例
* Subject：EventEmmiter的等价数据结构，可以当做Observable被监听，也可以作为Observaer发送新的事件。

## RxJS
Rx继承自响应式编程范式，已经在多种语言中实现，RxJS就是其在JavaScript层面上的实现。
### 创建Observable对象
* Rx.Observable.fromEvent(ele, event)
* new Observable()
* Observable.create()
```
let button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
  .subscribe(() => console.log(''clicked!'));

let Observable = new Observable(observer => {
    observer.next(value1);
    observer.next(value2);
    observer.error(err);
});

let observable = Rx.Observable.create(observer => {
    // ...
});
```

### RxJS和Promise的对比
能用Promise的场景RxJS都适用，RxJS是作为Promise的超集存在。RxJS的Observable可以通过toPromise()方法转为Promise对象。
Promise只能针对单一的异步事件进行resolve()操作，而在Observable中，不仅能处理一个单一的异步事件(即调用observer的next()方法)，而且能以流的形式响应多个异步事件。

### “冷”模式下的Observable
在Rx中，Observable通常可以实现成“热”模式或者“冷”模式。在“热"模式下，Observable对象一旦创建，便会开始发送数据。而在“冷”模式下，Observable对象会一直等到自己被订阅，才会开始数据流的发送。在RxJS中，Observable实现的是“冷”模式。
RxJS中还存在另外一种被称作Connectable的模式。这种模式下的Observable对象不管有没有被订阅，都不会发送数据，除非ConnectableObservable实例的connect()方法被调用。
```
let obs = new Observable(observer => {
    console.log('Observable start');
    observer.complate();
}).publish();   //publish()方法转为Connectable模式
console.log('start');
obs.subscribe();
console.log('after Observable has been subscried');
obs.connect();

//运行结果
start
after Observable has been subscried
Observable start
```

### RxJS中的Operator
* map
* filter()
* forkJoin()：把一个以上独立的Observable对象，合并为一个新的Observable对象
* concatMap()：如果某次数据请求需要依赖前一次请求的结果，即两个请求有先后顺序时用
* delay()
* timeout()：设置timeout，当超过这个时间没有返回数据流，便会抛出一个err
* debounceTime()
* disrinctUntilChanged()
* switchMap()：保证请求顺序