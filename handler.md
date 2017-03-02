# Handler原理
Handler原理也就是，Handler,Looper,MessageQueue一起配合工作的原理，Handler负责发送消息或者处理获取到的消息，MessageQueue负责管理Handler发送过来的消息，其内部实现是一个单链表。而Looper则会不停的读取MessageQueue中的消息，然后调用其dispatchMessage()方法，把消息分给对应得Handler去处理。
## Handler
handler的主要作用是发送消息和处理消息。
### 发送消息 
无论是SentMessage或者是Post一个Runable接口，发送消息最终会走到enqueueMessage()这个方法。
+ enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis)

```
   private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }
```
这个方法是将当前的消息处理者指向自己（target类型为Handler），然后再调用MessageQueue,将消息发送到消息队列中去。

### 处理消息
这是一个回调，被handler发送出去的消息都会在这里被处理。
+ public void dispatchMessage(Message msg)

```
    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }
```
首先判断msg.callback是否为Null,这个callback就是Runable接口,所以handleCallback(msg)也就是执行这个接口。

```
 private static void handleCallback(Message message) {
        message.callback.run();
    }
```
然后再判断mCallback是否为null,这个callback就不解释了如下。
```
  final Callback mCallback;

 public interface Callback {
        public boolean handleMessage(Message msg);
    }
```
最后走handleMessage(msg),这是一个空实现，但是其子类必须实现这个方法，来处理消息。

# MessageQueue
虽然叫做queue，但是内部却使用单链表去管理数据的。围绕其二个主要功能，添加Message和取出Message.
## 添加 Message
+ enqueueMessage(Message msg, long when)
截取其中部分代码，可以看到这一块很明显的是单链表的插入操作。
```
       if (p == null || when == 0 || when < p.when) {
                // New head, wake up the event queue if blocked.
                msg.next = p;
                mMessages = msg;
                needWake = mBlocked;
            } else {
                // Inserted within the middle of the queue.  Usually we don't have to wake
                // up the event queue unless there is a barrier at the head of the queue
                // and the message is the earliest asynchronous message in the queue.
                needWake = mBlocked && p.target == null && msg.isAsynchronous();
                Message prev;
                for (;;) {
                    prev = p;
                    p = p.next;
                    if (p == null || when < p.when) {
                        break;
                    }
                    if (needWake && p.isAsynchronous()) {
                        needWake = false;
                    }
                }
                msg.next = p; // invariant: p == prev.next
                prev.next = msg;
            }
```
## 取出 Message
取出Message主要是 next()方法,在这里是一个无限循环，如果单链表中没有Message,则会一直阻塞，如果有Message了，就会返回message，并且从单链表删除该message退出循环。
```
 for (;;) {
          ...
            if (nextPollTimeoutMillis != 0) {
                //获取不到Message那么就一直阻塞
                Binder.flushPendingCommands();
            }
                if (msg != null) {
                    if (now < msg.when) {
                        // Next message is not ready.  Set a timeout to wake up when it is ready.
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {
                        // Got a message.
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        //指针设为null,删除该节点
                        msg.next = null;
                        if (false) Log.v("MessageQueue", "Returning message: " + msg);
                        return msg;
                    }else{
                        //设置阻塞线程的标记
                        nextPollTimeoutMillis = -1;
                    }
```
# Looper
在子线程中必须调用Looper.prepare()来创建一个Looper对象使用,主线程（ActivityThread）已经创建，如果再调用Looper.prepare()就会报错。
```
  private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }
```
Looper创建时会获取当前线程的引用，并且会创建一个MessageQueue对象。
```
    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);
        mThread = Thread.currentThread();
    }
```