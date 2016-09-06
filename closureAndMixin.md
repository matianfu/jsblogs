# Closure vs Object

这是一个Object

```javascript
function Something(a, b) {
  this.x = a
  this.y = b
}

Something.prototype.sum = function() {
  return this.x + this.y
}

var z = new Something(1, 2)
console.log(z.sum())  // output 3
```

这是一个Closure

```javascript
function createSomething(a, b) {
  return {
    sum: function() {
      return a + b
    }
  }
}

var z = createSomething(1, 2)
console.log(z.sum()) // output 3
````

效果一样，但我想说的不是语法谁简洁的问题，而是这两者的区别很根本，而且重要。

前者的写法，也是大家熟悉和喜欢的，类似OO的，使用了this binding，后者使用了闭包变量；

在JS引擎的实际实现上，这两者的性能区别不好一概而论；仅从JS的语言定义上说，this binding是late binding，是JS的招牌特性之一；理论上late binding是动态的，提供了灵活性但是损失性能。

闭包变量，或者说lexical scope，是编译时可以解析的，闭包变量在创建时和函数的关系是固定的，类似early binding，理论上应该有更好的性能实际上不一定，看实现；

实际上在JS里Function Scope是可以当作Object来用的，它才是真正对应OO的成员变量的东西；闭包变量不共享，每次调用构造（工厂）函数时创建，而且绝对私有，外部无法访问（包括无法通过测试assert）。

然后我们来看重用；

ES6里提供了类似OO的Class语法，但只是一个语法糖，JS的继承被称为Prototypal继承；本质上它是一种共享。

比如我们有一个Document类，我们希望给它提供一个save方法；save方法本身需要有一个共享资源，例如文件句柄；从OO的角度说，把save方法放在基类里并不合适，因为它与Document自己的责任不符，通常是使用delegation；即每一个对象内有一个指针或者引用，指向了一个提供save服务的对象；

在JS里对应的逻辑，因为该外部服务对象是一个共享资源，所以它应该在prototype上被所有对象共享；

所以JS里对应的逻辑大概是：

```js
function createStore(filepath) {
  var stream = fs.createWriteStream(filepath)
  return {
    save: function(object) {
      stream.write(JSON.stringify(object))
      stream.write('\n')
    }
  }
}

var store = createStore('/home/somefile/to/save/objects')
```

然后在构造对象时，先构造它的原型
```js
function createDocumentPrototype(store) {
  return {
    store: store,
    save: function() {
      this.store.save(this) // or you want to save some props here
    },
    // other prototype methods here
  }
}
```

然后在构造对象时，使用Object.create
```js
function createDocument(proto, title, author, text) {
  return Object.create(proto, { title, author, text })
}
```



