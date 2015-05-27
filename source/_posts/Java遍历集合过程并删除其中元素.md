title: Java遍历集合过程并删除其中元素
date: 2015-05-27 15:36:06
tags: Java 集合 Exception ConcurrentModificationException
---
# 情景
---
&nbsp;&nbsp;&nbsp;&nbsp;可能我们都会有这样的需求，遍历集合的时候，对集合中某些元素做些处理，如删除，运行的时候会报java.util.ConcurrentModificationException异常。例如如下代码：

    public class Tester {
	
		public static class MyClass {
			public int num;
		}
		public static void main(String args[]) {
			Random random = new Random(new Date().getTime());
			ArrayList<MyClass> list = new ArrayList<>();
			for(int i = 0; i< 100; i++){
				MyClass myClass = new MyClass();
				myClass.num = random.nextInt(100) + 1;
				list.add(myClass);
			}
			for(MyClass myClass : list){
				if(myClass.num < 50){
					list.remove(myClass);
				}
			}
			for(int i = 0; i < list.size(); i++){
				System.out.print(list.get(i).num + " ");
			}
	
	        System.out.print("\n");
		}
	}

&nbsp;&nbsp;&nbsp;&nbsp;运行的时候会报错。出错信息如下：

	Exception in thread "main" java.util.ConcurrentModificationException
		at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:901)
		at java.util.ArrayList$Itr.next(ArrayList.java:851)
		at Tester.main(Tester.java:22)

---
# 原因分析
&nbsp;&nbsp;&nbsp;&nbsp;出错原因是checkForComodification中`modCount != expectedModCount`,抛出异常。

&nbsp;&nbsp;&nbsp;&nbsp;当我们调用`remove(myClass)`的时候，将使modCount++,从而使得`modCount != expectedModCount`成立。

# 解决方法
&nbsp;&nbsp;&nbsp;&nbsp;假如我们遍历集合的时候，就是要删除某些元素的时候，我们可以迭代器或者利用`CopyOnWriteArrayList`。

&nbsp;&nbsp;&nbsp;&nbsp;常用迭代器方法，我们可以将上面那段代码改写成如下所示：

	public class Tester {
	
		public static class MyClass {
			public int num;
		}
	
		public static void main(String args[]) {
			Random random = new Random(new Date().getTime());
			ArrayList<MyClass> list = new ArrayList<>();
			for (int i = 0; i < 100; i++) {
				MyClass myClass = new MyClass();
				myClass.num = random.nextInt(100) + 1;
				list.add(myClass);
			}
	
			Iterator<MyClass> iterator = list.iterator();
			while (iterator.hasNext()) {
				MyClass myClass = iterator.next();
				if (myClass.num < 50) {
					iterator.remove();
				}
			}
	
			for (int i = 0; i < list.size(); i++) {
				System.out.print(list.get(i).num + " ");
			}
	
			System.out.print("\n");
		}
	
	}

&nbsp;&nbsp;&nbsp;&nbsp;CopyOnWriteArrayList方法就请看这遍博文了[CopyOnWriteArrayList 解读](http://caoyaojun1988-163-com.iteye.com/blog/1754686)