title: AsyncTask并行问题
date: 2015-07-25 23:02:46
tags:
---

&nbsp;&nbsp;&nbsp;&nbsp;上次面试的时候，面试官问到AsyncTask是否并行，当时我回答说不支持，然后面试官后来又质问了我，我还是肯定地回答说不支持。最后面试官跟我说，真实是支持的。今天有时间，我又看了下AsyncTask的源码又看了Android Doc。文档是这么说的。

![Order of execution](http://i.imgur.com/RzHygHY.jpg)

&nbsp;&nbsp;&nbsp;&nbsp;另外我再贴下AsyncTask的源码：
	
	public static final Executor SERIAL_EXECUTOR = new SerialExecutor();
	private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;
	
	private static class SerialExecutor implements Executor {
        final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
        Runnable mActive;

        public synchronized void execute(final Runnable r) {
            mTasks.offer(new Runnable() {
                public void run() {
                    try {
                        r.run();
                    } finally {
                        scheduleNext();
                    }
                }
            });
            if (mActive == null) {
                scheduleNext();
            }
        }

        protected synchronized void scheduleNext() {
            if ((mActive = mTasks.poll()) != null) {
                THREAD_POOL_EXECUTOR.execute(mActive);
            }
        }
    }

&nbsp;&nbsp;&nbsp;&nbsp;一般我们都会调用execute这个方法，但这方法都直接调用了`executeOnExecutor(sDefaultExecutor, params)`,但我们细读SerialExecutor会发现，扔进这里的任务真实是串行的

&nbsp;&nbsp;&nbsp;&nbsp;最像Doc里说的那样，如果你要并行执行任务地话，调用这个方法`executeOnExecutor(Executor exec, Params... params)`,但我们得提供方法第一个参数，好在AsyncTask已经帮我们初始了一个Executor:

	public static final Executor THREAD_POOL_EXECUTOR
            = new ThreadPoolExecutor(CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE,
                    TimeUnit.SECONDS, sPoolWorkQueue, sThreadFactory);



&nbsp;&nbsp;&nbsp;&nbsp;[http://blog.csdn.net/hpb21/article/details/26965169](http://blog.csdn.net/hpb21/article/details/26965169 "Android源码分析—带你认识不一样的AsyncTask(串并行)")，这篇文章写地很好。

&nbsp;&nbsp;&nbsp;&nbsp;所以说，源码还是得多读，读懂。不要只知道一方面，要全盘掌握！