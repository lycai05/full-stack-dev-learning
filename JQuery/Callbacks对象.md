# 使用Callbacks对象
对函数的统一管理。我们先创建一个Callbacks对象，然后通过add函数添加两个函数foo和bar。执行fire函数后，foo和bar就会执行。
那么Callbacks的实现原理是是什么样的呢？
```javascript
function foo() {
    console.log('foo');
}

function bar() {
    console.log('bar');
}

cbs = $.Callbacks();
cbs.add(foo);
cbs.add(bar);
cbs.fire();
```

![](image-16.png)

# Callbacks的使用场景
有时候我们很难对不同作用域下的函数进行统一的管理。比如:
```javascript
function foo() {
    console.log('foo')
}
(function () {
  function bar() {
    console.log('bar')
  }  
    
})()

foo()
bar() // error
```
在外部作用域中我们无法访问bar函数。但是使用Callbacks，我们可以将上述代码改成如下形式：
```javascript
let cb = $.Callbacks()
function foo() {
    console.log('foo')
}
cb.add(foo);
(function () {
  function bar() {
    console.log('bar')
  }  
  cb.add(bar);
})()

cb.fire();
```
这样foo和bar就都可以被调用了

# Callbacks的参数
Callbacks接收一个参数options，options有四个可选的值：

1. once：回调函数列表只能被fire一次
2. memory：fire()后被添加至回调函数列表的函数也会被fire()
3. unique：同一个回调函数只能被添加一次
4. stopOnFalse：当一个回调函数返回false，则后续的回调函数都停止执行

1. once
```javascript

function foo() {
    console.log('foo');
}

function bar() {
    console.log('bar');
}

cbs = $.Callbacks();
cbs.add(foo);
cbs.add(bar);
cbs.fire();
cbs.fire();
```
如果不加‘once’，两次fire各自会执行并打印；加了'once'参数，只会fire一次

2. memory
```javascript
function foo() {
    console.log('foo');
}

function bar() {
    console.log('bar');
}

cbs = $.Callbacks('memory');
cbs.add(foo);
cbs.fire();
cbs.add(bar);
```
如果不加'memory'，bar不会打印；加了‘memory’，bar就会打印

3. unique
```javascript
function foo() {
    console.log('foo');
}

cbs = $.Callbacks('unique');
cbs.add(foo);
cbs.add(foo);
cbs.fire();
```
如果不加'unique'，foo会打印两次；加了'unique'，foo只会被打印一次

4. stopOnFalse
```javascript
function foo() {
    console.log('foo');
    return false
}

function bar() {
    console.log('bar');
}

cbs = $.Callbacks('stopOnFalse');
cbs.add(foo);
cbs.add(bar);
cbs.fire();
```
如果不加'stopOnFalse'，foo和bar都会被打印；加了'stopOnFalse'，bar不会被打印

5. 以上四个参数可以组合使用，写成字符串的形式，比如'once memory'