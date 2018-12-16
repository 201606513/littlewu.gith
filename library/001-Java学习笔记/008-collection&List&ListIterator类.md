# collection&List&ListIterator类

---
## collection类

### 对象数组的概述和使用

* 需求：我有5个学生，请把这个5个学生的信息存储到数组中，并遍历数组，获取得到每一个学生信息。

```
//int[] arr = new int[5];					//创建基本数据类型数组
Student[] arr = new Student[5];				//创建引用数据类型数组

arr[0] = new Student("张三", 23);				
//创建一个学生对象,存储在数组的第一个位置

arr[1] = new Student("李四", 24);				
//创建一个学生对象,存储在数组的第二个位置

arr[2] = new Student("王五", 25);				
//创建一个学生对象,存储在数组的第三个位置

for(int i = 0; i < arr.length; i++) {
	System.out.println(arr[i]);
}
```

*  数组和集合存储引用数据类型,存的都是地址值,
    * 把主类 .class 加载进内存，（在方法区中）
    * 主方法进栈，及main方法；发现student类,把它加载进内存。
    * 在栈中创建Student数组，然后再堆中分配一个地 址值（其中有5块）
    * 在栈中又一个与堆中对应的地址值，
    * 对象和实体存在堆中，有唯一的地址值对应。
    * 

### 集合的由来及集合继承体系图

* A:集合的由来
数组长度是固定,当添加的元素超过了数组的长度时需要对数组重新定义,太麻烦,java内部给我们提供了集合类,能存储任意对象,长度是可以改变的,随着元素的增加而增加,随着元素的减少而减少 
	
* B:数组和集合的区别
	* 区别1 : 
		* 数组既可以存储基本数据类型,又可以存储引用数据类型,基本数据类型存储的是值,引用数据类型存储的是地址值
		* 集合只能存储引用数据类型(对象)集合中也可以存储基本数据类型,但是在存储的时候会自动装箱变成对象
	* 区别2:
		* 数组长度是固定的,不能自动增长
		* 集合的长度的是可变的,可以根据元素的增加而增长

* C:数组和集合什么时候用
		* 1,如果元素个数是固定的推荐用数组
		* 2,如果元素个数不是固定的推荐用集合

* D:集合继承体系图

| collection 单列集合跟      |
| :----:  | 

| list有序（存和取得顺序一致）有索引，可以存储重复       |  set 无序（存和取得顺序不一致）无索引，不可以存储重复|
| :----:   |:----:  | 

| Vector      | ArrayList   |  LinkedList  | HashSet | TreeSet 
| :----:   | :----:  | :----:|:----:|:----:
| 数组实现     | 数组实现 |  链表实现     |哈希算法| 二叉树算法   

###  Collection集合的基本功能测试

* 基本功能
		
	boolean add(E e)
	boolean remove(Object o)
	void clear()
	boolean contains(Object o)
	boolean isEmpty()
	int size()

```
Collection c = new ArrayList();		
c.add("a");
c.add("b");
c.add("c");
c.add("d");

//c.remove("b");						//删除指定元素

//c.clear();								//清空集合

//System.out.println(c.contains("b"));		//判断是否包含

//System.out.println(c.isEmpty());  //空时为true

System.out.println(c.size()); 			//获取元素的个数
System.out.println(c);
```

* B:注意:
collectionXxx.java使用了未经检查或不安全的操作.
注意:要了解详细信息,请使用 -Xlint:unchecked重新编译.
java编译器认为该程序存在安全隐患
温馨提示:这不是编译失败,所以先不用理会,等学了泛型你就知道了

add方法如果是List集合一直都返回true,因为List集合中是可以存储重复元素的,如果是Set集合当存储重复元素的时候,就会返回false
ArrayList的父类的父类重写toString方法,所以在打印对象的引用的时候,输出的结果不是Object类中toString的结果

```
Collection c = new ArrayList();		//父类引用指向子类对象
boolean b1 = c.add("abc");
boolean b2 = c.add(true);						//自动装箱new Boolean(true);
boolean b3 = c.add(100);
boolean b4 = c.add(new Student("张三",23));			
boolean b5 = c.add("abc");

System.out.println(b1);true
System.out.println(b2);true
System.out.println(b3);true
System.out.println(b4);true
System.out.println(b5);true

System.out.println(c.toString());
```
	  
### 集合的遍历之集合转数组遍历

* A:集合的遍历
	* 其实就是依次获取集合中的每一个元素。
	* 把集合转成数组，可以实现集合的遍历
	* toArray()

字符串

```
Collection c = new ArrayList();
c.add("a");
c.add("b");
c.add("c");
c.add("d");

Object[] arr = c.toArray();						//将集合转换成数组
for(int i = 0; i < arr.length; i++) {
	System.out.println(arr[i]);
}
```

自定义对象

```
Collection c = new ArrayList();
c.add(new Student("张三", 23));				//Object obj = new Student("张三",23);
c.add(new Student("李四", 24));
c.add(new Student("王五", 25));
c.add(new Student("赵六", 26));

Object[] arr = c.toArray();					//将集合转换成数组
for (int i = 0; i < arr.length; i++) {
	//System.out.println(arr[i]);
	Student s = (Student)arr[i];			//向下转型
	System.out.println(s.getName() + "..." + s.getAge());
}
```

### Collection集合的带All功能测试

boolean addAll(Collection c)

```
Collection c1 = new ArrayList();
c1.add("a");
c1.add("b");
c1.add("c");
c1.add("d");

Collection c2 = new ArrayList();		
c2.add("a");
c2.add("b");
c2.add("c");
c2.add("d");

//c1.addAll(c2);							
//将c2中的每一个元素添加到c1中

c1.add(c2);									
//将c2看成一个对象添加到c1中

System.out.println(c1);


```

boolean removeAll(Collection c)

```
Collection c1 = new ArrayList();
c1.add("a");
c1.add("b");
c1.add("c");
c1.add("d");

Collection c2 = new ArrayList();
c2.add("a");
c2.add("b");
c2.add("z");

boolean b = c1.removeAll(c2);					//删除的是交集
System.out.println(b);
System.out.println(c1);


```

boolean containsAll(Collection c)

```
Collection c1 = new ArrayList();
c1.add("a");
c1.add("b");
c1.add("c");
c1.add("d");

Collection c2 = new ArrayList();
c2.add("a");
c2.add("b");
c2.add("z");

boolean b = c1.containsAll(c2);				
//判断调用的集合是否包含传入的集合
System.out.println(b);


```

boolean retainAll(Collection c)

```
Collection c1 = new ArrayList();
c1.add("a");
c1.add("b");
c1.add("c");
c1.add("d");

Collection c2 = new ArrayList();
c2.add("a");
c2.add("b");
c2.add("c");
c2.add("d");
c2.add("e");
c2.add("f");

//取交集后,如果调用的集合改变就返回true,如果调用的集合不变就返回false
boolean b = c1.retainAll(c2);					//取交集
System.out.println(b);
System.out.println(c1);
```

### 集合的遍历之迭代器遍历

* A:迭代器概述
	* 集合是用来存储元素,存储的元素需要查看,那么就需要迭代(遍历) 
* 迭代器的使用

```
Collection c = new ArrayList();
c.add("a");
c.add("b");
c.add("c");
c.add("d");

//对集合中的元素迭代(遍历)
Iterator it = c.iterator();					//获取迭代器

/*boolean b1 = it.hasNext();					
//判断集合中是否有元素,有就返回true
Object obj1 = it.next();
System.out.println(b1);
System.out.println(obj1);

boolean b2 = it.hasNext();					
Object obj2 = it.next();
System.out.println(b2);
System.out.println(obj2);*/

while(it.hasNext()) {
	System.out.println(it.next());
}
```

### Collection存储自定义对象并遍历

* Collection存储自定义对象并用迭代器遍历
	
```
Collection c = new ArrayList();
c.add(new Student("张三", 23));				
//添加的时候向上转型成了object ，Object obj = new Student("张三",23);
c.add(new Student("李四", 24));
c.add(new Student("王五", 25));
c.add(new Student("赵六", 26));

//获取迭代器
Iterator it = c.iterator();
while(it.hasNext()) {
	//System.out.println(it.next());
	Student s = (Student)it.next();			//向下转型
	System.out.println(s.getName() + "..." + s.getAge());
}
```

### 迭代器的原理及源码解析

* A:迭代器原理
	* 迭代器原理:迭代器是对集合进行遍历,而每一个集合内部的存储结构都是不同的,所以每一个集合存和取都是不一样,那么就需要在每一个类中定义hasNext()和next()方法,这样做是可以的,但是会让整个集合体系过于臃肿,迭代器是将这样的方法向上抽取出接口,然后在每个类的内部,定义自己迭代方式,这样做的好处有二,第一规定了整个集合体系的遍历方式都是hasNext()和next()方法,第二,代码有底层内部实现,使用者不用管怎么实现的,会用即可 
* B:迭代器源码解析
	* 1,在eclipse中ctrl + shift + t找到ArrayList类
	* 2,ctrl+o查找iterator()方法
	* 3,查看返回值类型是new Itr(),说明Itr这个类实现Iterator接口
	* 4,查找Itr这个内部类,发现重写了Iterator中的所有抽象方法 

## List类

### List集合

List集合的特有功能概述

* void add(int index,E element)

```
List list = new ArrayList();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
list.add(4, "f");						
//index<=size并且index>=0都不会报异常

//list.add(1,"e");
//list.add(10, "z");					
//java.lang.IndexOutOfBoundsException,当存储时使用不存在的索引时

System.out.println(list);
```

* E remove(int index)

```
List list = new ArrayList();
list.add("a");
list.add("b");
list.add("c");
list.add("d");

Object obj = list.remove(1);				
//通过索引删除元素,将被删除的元素返回
System.out.println(obj);
System.out.println(list);



List list = new ArrayList();
list.add(111);
list.add(222);
list.add(333);

list.remove(111);							
//删除的时候不会自动装箱,把111当作索引
System.out.println(list);
```

* E get(int index)

```
List list = new ArrayList();
list.add("a");
list.add("b");
list.add("c");
list.add("d");

//Object obj1 = list.get(2);
//System.out.println(obj1);

//通过索引遍历List集合
for(int i = 0;i < list.size(); i++) {
	System.out.println(list.get(i));
}


```

* E set(int index,E element)

```
List list = new ArrayList();
list.add("a");
list.add("b");
list.add("c");
list.add("d");

list.set(1, "z");						
//将指定位置的元素修改
System.out.println(list);
```

### List集合存储学生对象并遍历

* 通过size()和get()方法结合使用遍历。

```
List list = new ArrayList();
list.add(new Student("张三", 23));					
//Object obj = new Student("张三",23);
list.add(new Student("李四", 24));
list.add(new Student("王五", 25));
list.add(new Student("赵六", 26));

for(int i = 0; i < list.size(); i++) {
	//System.out.println(list.get(i));	 			
	//通过索引获取每一个元素
	Student s = (Student)list.get(i);
	System.out.println(s.getName() + "..." + s.getAge());
}
```

### 并发修改异常产生的原因及解决方案

* 需求：我有一个集合，请问，我想判断里面有没有"world"这个元素，如果有，我就添加一个"javaee"元素，请写代码实现。

```
//Object obj = new String();
list.add("a");										
list.add("b");
list.add("world");
list.add("c");
list.add("d");
list.add("e");

Iterator it = list.iterator();		/获取迭代器
while(it.hasNext()) {							
    
    //判断集合中是否有元素
	String str = (String)it.next();				//向下转型
	if("world".equals(str)) {
		list.add("javaee");						
		//遍历的同时在增加元素,并发修改ConcurrentModificationException
	}
}
```

* ConcurrentModificationException出现(并非修改异常)
	* 迭代器遍历，集合修改集合
 
* 解决方案
	* a:迭代器迭代元素，迭代器修改元素(ListIterator的特有功能add)
	* b:集合遍历元素，集合修改元素

```
//如果想在遍历的过程中添加元素,可以用ListIterator中的add方法
ListIterator lit = list.listIterator();			
//获取迭代器(List集合特有的)
while(lit.hasNext()) {
	String str = (String)lit.next();			//向下转型
	if("world".equals(str)) {
		lit.add("javaee");
	}
}
```

### ListIterator

* boolean hasNext()是否有下一个
* boolean hasPrevious()是否有前一个
* Object next()返回下一个元素
* Object previous();返回上一个元素

```
List list = new ArrayList();
list.add("a");								
//Object obj = new String();
list.add("b");
list.add("world");
list.add("c");
list.add("d");
list.add("e");

ListIterator lit = list.listIterator();	 //获取迭代器
while(lit.hasNext()) {
    //获取元素并将指针向后移动
	System.out.println(lit.next()); 			
}
//输出结果a  b   world   c   d   e

System.out.println("-----------------");

while(lit.hasPrevious()) {
    //获取元素并将指针向前移动
	System.out.println(lit.previous()); 		
}
//输出结果e     d    c   world   b   a

```

### Vector的特有功能

* public void addElement(E obj)
* public E elementAt(int index)
* public Enumeration elements()

```
Vector v = new Vector();				//创建集合对象,List的子类
v.addElement("a");
v.addElement("b");
v.addElement("c");
v.addElement("d");

//Vector迭代
Enumeration en = v.elements();			//获取枚举
while(en.hasMoreElements()) {			//判断集合中是否有元素
	System.out.println(en.nextElement());//获取集合中的元素
}
```

###  数据结构之数组和链表

* A:数组
	* 查询快修改也快
	* 增删慢
	
* B:链表
	* 查询慢,修改也慢
	* 增删快

### List的三个子类的特点

List的三个子类的特点

* ArrayList:
	* 底层数据结构是数组，查询快，增删慢。
	* 线程不安全，效率高。

* Vector:
	* 底层数据结构是数组，查询快，增删慢。
	* 线程安全，效率低。
    * Vector相对ArrayList查询慢(线程安全的)
    * Vector相对LinkedList增删慢(数组结构)

* LinkedList:
	* 底层数据结构是链表，查询慢，增删快。
	* 线程不安全，效率高。

* Vector和ArrayList的区别
	* Vector是线程安全的,效率低
	* ArrayList是线程不安全的,效率高
    * 共同点:都是数组实现的

* ArrayList和LinkedList的区别
	* ArrayList底层是数组结果,查询和修改快
	* LinkedList底层是链表结构的,增和删比较快,查询和修改比较慢
    * 共同点:都是线程不安全的

* List三个子类到底使用谁呢?
	* 查询多用ArrayList
	* 增删多用LinkedList
	* 如果都多ArrayList

	
	
学习来源：伟大的黑马