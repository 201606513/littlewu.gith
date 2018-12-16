# File类

---

## File类

### File类的概述

* File更应该叫做一个路径
	* 文件路径或者文件夹路径  
	* 路径分为绝对路径和相对路径
	* 绝对路径是一个固定的路径,从盘符开始
	* 相对路径相对于某个位置,在eclipse下是指当前项目下,在dos下
* 查看API指的是当前路径
* 文件和目录路径名的抽象表示形式

### File类的构造方法
* File(String pathname)：根据一个路径得到File对象

* File(String parent, String child):根据一个目录和一个子文件/目录得到File对象

* File(File parent, String child):根据一个父File对象和一个子文件/目录得到File对象

```
File file = new File("D:/自学/笔记/Java异常&File递归.docx");
System.out.println(file.exists());

String parent = "D:/自学/笔记/";
String child = "Java异常&File递归.docx";
File file = new File(parent,child);
System.out.println(file.exists());

File parent = new File("D:/自学/笔记/");
String child = "Java异常&File递归.docx";
File file = new File(parent, child);

```

### File类的创建功能

* public boolean createNewFile():创建文件 如果存在这样的文件，就不创建了
	
```
File file = new File("yyy.txt");
System.out.println(file.createNewFile());			//如果没有就创建,返回true

File file2 = new File("zzz");
System.out.println(file2.createNewFile());
```
	
* public boolean mkdir():创建文件夹 如果存在这样的文件夹，就不创建了
	
```
File dir1 = new File("aaa");
System.out.println(dir1.mkdir());

File dir2 = new File("bbb.txt");				//这样写是可以的,文件夹也是可以有后缀的
System.out.println(dir2.mkdir());
```
* public boolean mkdirs():创建文件夹,如果父文件夹不存在，会帮你创建出来
	
```
File dir3 = new File("ccc\\ddd");
System.out.println(dir3.mkdirs()); 				//创建多级目录
```

*  注意事项：如果你创建文件或者文件夹忘了写盘符路径，那么，默认在项目路径下。

		
### File类的重命名和删除功能

* public boolean renameTo(File dest):把文件重命名为指定的文件路径

```
File file1 = new File("ooo.txt");
File file2 = new File("D:\\xxx.txt");
System.out.println(file1.renameTo(file2));

```

* public boolean delete():删除文件或者文件夹

```
File file1 = new File("yyy.txt");
System.out.println(file1.delete());

File file2 = new File("aaa");
System.out.println(file2.delete());

File file3 = new File("ccc");					//如果删除一个文件夹,那么文件夹必须是空的
System.out.println(file3.delete());
```

* 重命名注意事项
	* 如果路径名相同，就是改名。
	* 如果路径名不同，就是改名并剪切。
* 删除注意事项：
	* Java中的删除不走回收站。
	* 要删除一个文件夹，请注意该文件夹内不能包含文件或者文件夹


### File类的判断功能

* public boolean isDirectory():判断是否是目录
* public boolean isFile():判断是否是文件
* public boolean exists():判断是否存在
* public boolean canRead():判断是否可读
* public boolean canWrite():判断是否可写
* public boolean isHidden():判断是否隐藏

```
public static void main(String[] args) {
	//demo1();
	File file = new File("zzz");
	file.setReadable(false);
	//windows系统认为所有的文件都是可读的
	System.out.println(file.canRead());

	file.setWritable(true);
	//windows系统可以设置为不可写
	System.out.println(file.canWrite());
	
	File file2 = new File("aaa.txt");
	System.out.println(file2.isHidden());	//判断是否是隐藏文件
	System.out.println(file.isHidden());
}

public static void demo1() {
	File dir1 = new File("ccc");
	System.out.println(dir1.isDirectory());	//判断是否是文件夹
	
	File dir2 = new File("zzz");
	System.out.println(dir2.isDirectory());
	
	System.out.println(dir1.isFile());		//判断是否是文件
	System.out.println(dir2.isFile());
}
```

### File类的获取功能

* public String getAbsolutePath()：获取绝对路径
* public String getPath():获取路径
* public String getName():获取名称
* public long length():获取长度。字节数
* public long lastModified():获取最后一次的修改时间，毫秒值
* public String[] list():获取指定目录下的所有文件或者文件夹的名称数组
* public File[] listFiles():获取指定目录下的所有文件或者文件夹的File数组 

```
File file1 = new File("Java异常&File递归.docx");
File file2 = new File("D:/自学/笔记/Java异常&File递归.docx");
System.out.println(file1.getAbsolutePath());	//获取绝对路径
System.out.println(file2.getAbsolutePath());

System.out.println(file1.getPath());  //获取构造方法中传入路径
System.out.println(file2.getPath());

System.out.println(file1.getName());  //获取文件或者文件的名字
System.out.println(file2.getName());

System.out.println(file1.length());

Date d = new Date(file1.lastModified()); //文件的最后修改时间
SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
System.out.println(sdf.format(d));

File dir = new File("D:/自学/笔记");
String[] arr = dir.list();		//仅为了获取文件名
for (String string : arr) {
	System.out.println(string);
}

File[] subFiles = dir.listFiles();
for (File file : subFiles) {	//获取文件对象(全路径)
	System.out.println(file);
}

```

### 输出指定目录下指定后缀的文件名
*  判断E盘目录下是否有后缀名为.jpg的文件，如果有，就输出该文件名称

```
public static void main(String[] args) {
	File dir = new File("E:\\");
	
	//方法一
	//获取e盘下所有的文件或文件夹
	String[] arr = dir.list();					
	for (String string : arr) {
		if(string.endsWith(".jpg")) {
			System.out.println(string);
		}
	}
	
	
	//方法二
	//获取e盘下所有的文件或文件夹对象
	File[] subFiles = dir.listFiles();			
	for (File subFile : subFiles) {
	   if(subFile.isFile() && subFile.getName().endsWith(".jpg")) {
			System.out.println(subFile);
		}
	}
```

### 文件名称过滤器的概述及使用

* 文件名称过滤器的概述
	* public String[] list(FilenameFilter filter)
	* public File[] listFiles(FileFilter filter)
* 文件名称过滤器的使用
	* 需求：判断E盘目录下是否有后缀名为.jpg的文件，如果有，就输出该文件名称
* 源码分析
	* 带文件名称过滤器的list()方法的源码

```
public static void main(String[] args) {
	File dir = new File("E:\\");
	
	String[] arr = dir.list(new FilenameFilter() {
		@Override
		public boolean accept(File dir, String name) {
			//System.out.println(dir);
			//System.out.println(name);
			File file = new File(dir, name);
			return file.isFile() && file.getName().endsWith(".jpg");
		}
	});
	
	for (String string : arr) {
		System.out.println(string);
	}
}

```

学习来源：伟大的黑马


