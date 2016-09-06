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

我想说的不是语法谁简洁的问题，而是这两者的区别很根本而且重要。

前者的写法，也是大家熟悉和喜欢的，类似OO的，使用了this binding，而后者使用了闭包变量；

在JS引擎的实际实现上，这两者的性能区别不好一概而论优劣，仅从JS的语言定义上说，this binding是late binding，也是JS的招牌特性之一，
