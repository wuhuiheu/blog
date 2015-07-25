title: Handler HandlerThread AsyncQueryHandler理解
date: 2015-07-16 11:51:31
tags: [Android, Handler, HandlerThread, AsyncQueryHandler]
---
&nbsp;&nbsp;&nbsp;&nbsp;[http://blog.csdn.net/stonecao/article/details/6417364](http://blog.csdn.net/stonecao/article/details/6417364 "Handler机制")

&nbsp;&nbsp;&nbsp;&nbsp;[http://blog.csdn.net/feiduclear_up/article/details/46840523](http://blog.csdn.net/feiduclear_up/article/details/46840523 " Android HandlerThread 源码分析")

&nbsp;&nbsp;&nbsp;&nbsp;[http://www.cnblogs.com/jisheng/archive/2012/01/04/2311680.html](http://www.cnblogs.com/jisheng/archive/2012/01/04/2311680.html "AsyncQueryHandler异步处理框架")

&nbsp;&nbsp;&nbsp;&nbsp;[http://blog.csdn.net/wanglong0537/article/details/6304239](http://blog.csdn.net/wanglong0537/article/details/6304239 "例子")


&nbsp;&nbsp;&nbsp;&nbsp;HandlerThread：因为一个Thread只能运行一次，如果调用多次Thread.start()将会报错。例如：
	
	package test;
	public class Test {
		public static void main(String[] args) throws Exception {
	        thread.start();
	        TimeUnit.SECONDS.sleep(1);
	        thread.start();
	    }
	
	    private static int count = 0;
	    private static Thread thread = new Thread() {
	        public void run() {
	            System.out.println(count++ + "");
	        };
	    };

	}

将直接抛`Exception in thread "main" java.lang.IllegalThreadStateException`异。

&nbsp;&nbsp;&nbsp;&nbsp;所以如果一个thread将会被循环利用，但又想避免多次new thread资源消耗。可以用下HandlerThread.

