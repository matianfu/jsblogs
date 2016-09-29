```javascript
class Thumbnailer {                                                                                                 
  
  constructor(thumbdir, tmpdir) {                                                                                    
    this.working = []                                                                                               
    this.pending = []                                                                                               
  }                                                                                                            
  
  request(digest, opts, callback) {                                                                                 
    
    let job, key = digest + hash(opts)                                                                                   
    
    // check if in working
    job = this.working.find(w => w.key === key)                                                                     
    if (job) { 
      if (!callback) {
        // non-blocking, synchronous return                                                            
        return { status: 'working' }                                                                                
      }

      // asynchronous return, trigger when job done
      job.callbacks.push(callback)                                                                                  
      return                                                                                                        
    }
    ...
  }
```

这是代码的一部分；这段代码奇怪的地方是，`request`函数既是同步又是异步的，判断依据是是否提供了callback函数；

代码功能是启动一个生成缩略图的job；客户端的请求里有一个nonblocking参数，如果nonblocking为true，就意味着如果server端还没有计算出缩略图，应立刻返回202
