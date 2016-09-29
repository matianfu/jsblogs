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

代码功能是启动一个生成缩略图的job；客户端的请求里有一个nonblocking参数，如果nonblocking为true，意思是如果server端还没有计算出缩略图，应立刻返回202，同时给出status；

调用request的函数是一个callback异步函数（从http/express router进来的），它首先直接去找预先生成的缩略图文件，如果没有就调用这个Thumbnailer类的Singleton实例的request方法；

在调用request之前调用者要判断一下客户端请求要求的是blocking还是nonblocking；如果是blocking请求，在调用request的时候要提供callback，如果该job已经存在，callback会被push到job的callback队列里；如果job尚未存在或者处于pending状态，thumbnailer会立刻创建一个计算任务，同时把callback放入该任务的callback队列，那么这些请求就会被block到计算任务完成，然后返回；

如果是nonblocking请求，调用者在调用request的时候不要提供callback；request函数如果在working queue或者pending queue中找到了相同任务，会用return返回status对象（或者错误），如果没有找到相同任务，会立刻创建一个working或者pending的job，但是同样同步返回status。

所以这个函数看起来就这么奇怪，既可以同步返回也可以异步返回，本质上是个同步的，如果仅仅把callback当作一个参数来看，但它也确实可以block异步调用，如果这是客户端希望的功能；而且Thumbnailer类也可以支持abort方法，在整个类的abort方法被调用时，把所有working queue里的callback都用error返回了。

实际上这就是内核里的io经常干的事儿，虽然代码看上去奇怪，但是到目前为止，我还是认为这样的写法是合理的。



