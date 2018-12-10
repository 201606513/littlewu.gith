# Object类

---

### 1_API概述
* A:API(Application Programming Interface)
	* 应用程序编程接口
* B:Java API
	* 就是Java提供给我们使用的类，这些类将底层的实现封装了起来

### 2_Object类的概述
* A:Object类概述
	* 类层次结构的根类
	* 所有类都直接或者间接的继承自该类
* B:构造方法
	* public Object()
	* 回想面向对象中为什么说：
	* 子类的构造方法默认访问的是父类的无参构造方法

### 3_Object类的hashCode()方法

* public int hashCode()
* a:返回该对象的哈希码值。默认情况下，该方法会根据对象的地址来计算。
* b:不同对象的，hashCode()一般来说不会相同。但是，同一个对象的hashCode()值肯定相同。

### 4_Object类的getClass()方法

* public final Class getClass()
* a:返回此 Object 的运行时类。
* b:可以通过Class类中的一个方法，获取对象的真实类的全名称。
* public String getName()

```
Student s = new Student("张三", 23);
// Class clazz = new Class();

Class clazz = s.getClass();//获得该对象的字节码文件，.class
String name = clazz.getName(); //获得该对象的的名称，完全限定名
System.out.println(name);

```

### 5_Object类的toString()方法

* public String toString()
* a:返回该对象的字符串表示。
* b:它的值等于：
	* getClass().getName() + "@" + Integer.toHexString(hashCode())
* c:由于默认情况下的数据对我们来说没有意义，一般建议重写该方法。
```
/**
 * @param args
 * com.heima.bean.Student@bcda2d
 * public Stirng toString() {
	return  getClass().getName() + "@"+Integer.toHexString(hashCode())
	}
	左边 ：类名
	中间：@
	右边：hashCode的十六进制表现形式
	toString方法的作用:可以更方便的显示属性值。
	getXxx方法是为了获取值，可以显示也可以赋值，或者其他操作。
 */
public static void main(String[] args) {

	Student s = new Student("张三", 23);
	String str = s.toString();
	System.out.println(str); //com.heima.bean.Student@bcda2d

	System.out.println(s.toString());
	System.out.println(s);
	// 如果直接打印对象的引用，默认打印对象的toString()方法，
	System.out.println("我的姓名是：" + s.getName() + ",我的年龄是："+ s.getAge());
}
```
### 6_Object类的equals()方法

**Object的equals方法**
比较两个对象是否相等
```
public boolean equals(Object obj){
	  return (this == obj);
}
```
* a:指示其他某个对象是否与此对象“相等”。
* b:默认情况下比较的是对象的引用是否相同。
* c: Object中的equals方法是比较对象的地址值，没有什么意义，我们 需要重写它。 因为在开发中我们通常比较的是对象中的属性值，我们认为 相同属性是同一个对象，这样我们就需要重写equals方法。

**重写equals()方法**
```
//多态
@Override
//重写equals方法
public boolean equals(Object obj) {
	//传入的对象发生了向上转型，要想使用子类的方法需要向下转型
	Student  s = (Student)obj;
	return this.name.equals(s.name) &&this.age == s.age;
}
```

### 7_==号和equals方法的区别
**共同点**：都可以做比较，返回值都是boolean

**区别：**

* ==是一个比较运算符号,既可以比较基本数据类型,也可以比较引用数据类型,基本数据类型比较的是值,引用数据类型比较的是地址值
* equals方法是一个方法,只能比较引用数据类型,所有的对象都会继承Object类中的方法,如果没有重写Object类中的equals方法,equals方法和==号比较引用数据类型无区别,重写后的equals方法比较的是对象中的属性

学习来源：伟大黑马
