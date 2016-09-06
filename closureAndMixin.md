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
```
