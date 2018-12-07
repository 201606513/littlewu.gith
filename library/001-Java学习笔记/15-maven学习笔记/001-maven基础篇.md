# maven基础篇

-----

maven是一个项目管理工具，使用maven对Java项目进行构建和依赖管理。

---

## 1、传统与maven对比

### 1.1 项目构建
>* 传统 ： 要对工程中的源代码进行编译，及把Java文件编译成class文件。执行测试，打成war包部署Tomcat。
>* maven : 把将项目的每一个阶段进行了标准化。项目在编码完成后，对项目进行编译、测试、打包、部署等一系列的操作都通过命令来实现
>* 好处： 命令完成构建、编译、打包，方便快捷；方便团队多人进行开发。

### 1.2 依赖管理
>* 传统：需要人工进行导包，容易造成包冲突，而且会导致项目会很大。
>* maven 依赖管理：只需要在pom.xml中添加jar包的坐标即可，而且maven会自动卸载jar包。
>* 好处：maven管理jar包，避免了版本冲突；自动下载jar包。

### 1.3 总评maven
>* 对项目标准化，一步构建。
>* 依赖管理，自动下载jar包。
>* 跨平台，Linux可用。
>* maven构建项目，降低了项目的维护成本。

## 2、 maven介绍

### 2.1 下载安装

>* 前提：maven程序java开发，它的运行依赖jdk。所以要先安装配置JDK
>* 下载官网：http://maven.apache.org/download.cgi
>* 配置：
    - 配置 MAVEN_HOME ---> D:\apache-maven-3.5.0(路径）
    - path环境变量%MAVEN_HOME%/bin ----> path
>* 检查是否安装成功：mvn -v ---->看是否出现maven版本号与jdk版本

### 2.2 目录介绍
>* bin 可执行的脚本命令，mvn.bat(以run运行项目)、mvnDebug.bat（以debug方式运行项目）
>* boot maven运行需要的类加载器
>* conf 配置文件中心
>* lib maven运行依赖jar包

### 2.3 maven仓库
>* 本地仓库：在我们计算机上存jar包的地方，当你需要某个jar包时回去远程仓库或者中央仓库下载，并存在这里。默认在Windows用户下的.m2/repository
>* 远程仓库： 可以是互联网或者是局域网内。
>* 中央仓库：即jar中部，http://repo1.maven.org/maven2

### 2.4 配置本地仓库
>* 目的:让maven程序知道仓库在哪
>* 配置：在conf/setting.xml
    ```<localRepository>本地仓库路径</localRepository>```

### 2.5 maven的常用命令
>*	clean : 清理 ---> 将项目根目录下target目录清理掉。
>*	compile : 编译  --->  将项目中.java文件编译为.class文件
>*	test : 单元测试  ---> 单元测试类名有要求：XxxxTest.java
将项目根目录下src/test/java目录下的单元测试类都会执行。
>*	package : 打包
web project  ---- war包
java project  -----jar包
 ---> 将项目打包，打包项目根目录下taget目录
>*	install : 安装  ---> 解决本地多个项目公用一个jar包。打包到本地仓库

### 2.6 maven项目工程目录
工程根目录下就只有src和target两个目录
>* src
    - main
      –bin 脚本库
      –java java源代码文件
      –resources 资源库，会自动复制到classes目录里
      –filters 资源过滤文件
      –assembly 组件的描述配置（如何打包）
      –config 配置文件
      –webapp web应用的目录。WEB-INF、css、js等
    - test
      –java 单元测试java源代码文件
      –resources 测试需要用的资源库
      –filters 测试资源过滤库
>* target
target是有存放项目构建后的文件和目录，jar包、war包、编译的class文件等。target里的所有内容都是maven构建的时候生成的

### 2.7 maven生命周期
Maven拥有三套相互独立的生命周期，它们分别为clean，default和site。 每个生命周期包含一些阶段，这些阶段是有顺序的，并且后面的阶段依赖于前面的阶段，用户和Maven最直接的交互方式就是调用这些生命周期阶段。

以clean生命周期为例，它包含的阶段有pre-clean, clean 和 post clean。当用户调用pre-clean的时候，只有pre-clean得以执行，当用户调用clean的时候，pre-clean和clean阶段会得以顺序执行；当用户调用post-clean的时候，pre-clean,clean,post-clean会得以顺序执行。

较之于生命周期阶段的前后依赖关系，三套生命周期本身是相互独立的，用户可以仅仅调用clean生命周期的某个阶段，或者仅仅调用default生命周期的某个阶段，而不会对其他生命周期产生任何影响。

>* clean 生命周期
     - clean生命周期的目的是清理项目，它包含三个阶段：
     1）pre-clean 执行一些清理前需要完成的工作。
     2）clean 清理上一次构建生成的文件。
     3）post-clean 执行一些清理后需要完成的工作。

>* default 生命周期
    - default生命周期定义了真正构件时所需要执行的所有步骤，它是生命周期中最核心的部分，它包含的阶段如下：
       1） validate 验证项目是否正确和所有需要的相关资源是否可用
       2） initialize 初始化构建
       3） generate-sources
       4)   process-sources 处理源代码
       5） generate-resources 
       6)   process-resources 处理项目主资源文件。对src/main/resources目录的内容进行变量替换等工作后，复制到项目输出的主classpath目录中。
       7） compile 编译项目的主源代码
       8） process-classes
       9)   generate-test-sources
       10) process-test-sources 处理项目测试资源文件
      11）generate-test-resources
       12)  process-test-resources 处理测试的资源文件
       13）test-compile 编译项目的测试代码
       14）process-test-classes
       15)  test 使用单元测试框架运行测试，测试代码不会被打包或部署
       16）prepare-package 做好打包的准备
       17）package 接受编译好的代码，打包成可发布的格式
       18)  pre-integration-test
       19)  integration-test
       20)  post integration-test
       21)  verify
       22)  install 将包安装到Maven本地仓库，供本地其他Maven项目使用
       23）deploy 将最终的包复制到远程仓库，供其他开发人员和Maven项目使用

>* site 生命周期
    site生命周期的目的是建立和发布项目站点，Maven能够基于POM所包含的信息，自动生成一个友好的站点，方便团队交流和发布项目信息。该生命周期包含如下阶段：
      1）pre-site 执行一些在生成项目站点之前需要完成的工作
      2）site 生成项目站点文档
      3）post-site 执行一些在生成项目站点之后需要完成的工作
      4）site-deploy 将生成的项目站点发布到服务器上








