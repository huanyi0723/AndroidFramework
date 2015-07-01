# 1 进程线程管理
1 进程 程序的一个运行实例
	线程 CPU调度的基本单位
2 主线程ActivityThread
一个Activity启动后 有三个线程 一个ActivityThread 两个Binder Thread
Service寄存于ActivityThread之中 启动流程与Activity基本一致
启动Service 同样需要两个Binder Thread
对于同一AndroidManifest.xml中定义的四大组件 除非有特别声明 否则运行于同一个线程中
不同包中的组件可通过一定方式运行在一个进程空间中 android:process

2 Hander MessageQueue Runnable Looper
Looper不断获取MessageQueue中的一个Message 然后由Handler来处理
Handler
	final MessageQueue mQueue;
    final Looper mLooper;
    final Callback mCallback;
每个Thread对应一个Looper 每个Looper对应一个MessageQueue
每个MessageQueue中有n个Message 每个Message最多指定一个Handler来处理
Thread与Handler的关系 
	一对多的关系
Handler的作用
	1 处理Message
	handleMessage(Message msg) //对Message进行处理
	dispatchMessage(Message msg) //对Message进行分发
	
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
	
	2 将某个Message压入MessageQueue中
	Post系列
		post(Runnable r)
		postAtTime(Runnable r, long uptimeMillis)
	Send系列
		sendMessage(Message msg)
		sendEmptyMessage(int what)
		sendMessageDelayed(Message msg, long delayMillis)
	Post与Send的区别 Post需要将其它类型信息转换为Message 再调用Send
	
	 public final boolean post(Runnable r) {
       return  sendMessageDelayed(getPostMessage(r), 0);
    }
	
	private static Message getPostMessage(Runnable r) {
        Message m = Message.obtain(); //全局的Message池 许使用时通过obtain()获得 不是自行创建 避免不必要资源浪费
        m.callback = r; //将Runnable对象设置为Message的回调函数
        return m;
    }
	
	 public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        MessageQueue queue = mQueue; //Handler对应消息队列
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, uptimeMillis); //将消息压入MessageQueue中
    }

MessageQueue 消息队列
		新建队列
			MessageQueue(boolean quitAllowed) {
				mQuitAllowed = quitAllowed;
				mPtr = nativeInit(); //在本地新建一个NativeMessageQueue对象 赋值给mPtr
			}
		元素入队
			enqueueMessage(Message msg, long when)
		元素出队
			 next()
		删除元素
			removeMessages(Handler h, int what, Object object)
			removeMessages(Handler h, Runnable r, Object object)
		销毁队列
			private void dispose() {
				if (mPtr != 0) {
					nativeDestroy(mPtr);
					mPtr = 0;
				}
			}

Looper
	应用程序使用Looper分两种情况
		主线程 ActivityThread
		普通线程
			class LooperThread extends Thread{
				public Hander mHandler;
				public void run(){
					Looper.prepare(); //做了些什么公司
					mHandler = new Hander(){
						public void handleMessage(Message msg){
							//处理消息
						}
					};
					Looper.loop(); //进入主循环
				}
			}
			
		static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
		
3 UI主线程 ActivityThread
public static void main(String[] args) {
        SamplingProfilerIntegration.start();
        CloseGuard.setEnabled(false);
        Environment.initForCurrentUser();
        EventLogger.setReporter(new EventLoggingReporter());
        Security.addProvider(new AndroidKeyStoreProvider());
        Process.setArgV0("<pre-initialized>");
		//主线程调用prepareMainLooper() 普通线程调用prepare()
        Looper.prepareMainLooper();

        ActivityThread thread = new ActivityThread(); //mian()是static 在这里需要创建一个实例
        thread.attach(false);//Activity有界面显示 该函数与WindowManagerService建立联系

        if (sMainThreadHandler == null) {
            sMainThreadHandler = thread.getHandler(); //主线程对应的Handler
        }

        AsyncTask.init();

        if (false) {
            Looper.myLooper().setMessageLogging(new
                    LogPrinter(Log.DEBUG, "ActivityThread"));
        }

        Looper.loop(); //主循环开始

        throw new RuntimeException("Main thread loop unexpectedly exited"); //运行到这里 说明退出了上面的Looper循环
    }
}

Looper中的loop()
不断从消息队列中取出需要处理的事件 然后分发给相关责任人
public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue; //自带消息队列

        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();

		//消息循环开始
        for (;;) {
            Message msg = queue.next(); // 取出一个消息 可能会阻塞
            if (msg == null) { //没有消息说明要退出了
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            msg.target.dispatchMessage(msg); //分派消息

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            // Make sure that during the course of dispatching the
            // identity of the thread wasn't corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }

            msg.recycle(); //消息处理完回收
        }
    }


4 Thread类
Thread implements Runnable
public interface Runnable {
    public void run();
}
使用Thread的方法
MyThread thr = new MyThread{}
thr.start();

new Thread(Runnable target).start();
public synchronized void start() {
        checkNotStarted();
        hasBeenStarted = true;
        VMThread.create(this, stackSize); //真正创建一个CPU线程
    }
	
线程状态
public enum State {
        NEW, //已创建未start
        RUNNABLE, //可运行
        BLOCKED, //阻塞
        WAITING, //等待
        TIMED_WAITING, //等待特定的时间
        TERMINATED //终止运行
    }
	
线程控制方法
wait() notify() notifyAll() interrupt() join() sleep()

应用程序的启动流程
在Launcher点击相应的应用图标启动
通过startActivity启动