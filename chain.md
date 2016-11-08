```javascript
// for human being
const encodingIndex1 = (enc) => {
  let index = [
    'en_US', 
    'zh_CN'
  ].indexOf(enc)
  return index === -1 ? 0 : index
}

console.log(encodingIndex1('en_US'))
console.log(encodingIndex1('zh_CN'))
console.log(encodingIndex1('ja_JP'))

// for functional freaks, C for chain 
const C = x => f => f ? C(f(x)) : x

const encodingIndex2 = enc => C(enc)
  (x => ['en_US', 'zh_CN'].indexOf(x))
  (i => i === -1 ? 0 : i)

console.log(encodingIndex2('en_US')())
console.log(encodingIndex2('zh_CN')())
console.log(encodingIndex2('ja_JP')())
```
