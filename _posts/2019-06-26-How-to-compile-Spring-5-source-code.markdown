---
layout: post
title: How to compile Spring 5 source code
date: 2019-06-26 14:13:24 +0800
excerpt: This blog describes in detail the Spring 5 source compilation process.
author: 霜月初六
img:  # Add image post (optional)
tags: [Spring]
---



## 如何编译Spring 5源码

想要阅读Spring源码，那就得先对源码进行编译，然后编写测试代码进入到源码中一步一步的调试。

当然也可以直接依赖Spring，然后在反编译的文件中进行调试，但这样不好的地方在于你不能更改源码，而且调试也比较麻烦

### 一、环境准备

>Windows 10系统
>spring-framework-5.0.x 
>Gradle4.9				 
>jdk1.8.0_121
>IntelliJ IDEA 2018.3.5

#### 1、下载spring-framework-5.0.x

GitHub下载地址：<https://github.com/spring-projects/spring-framework/tree/5.0.x>

直接点击右边绿色按钮，然后点击`Download ZIP`就可以将源码安装包下载到本地

#### 2、下载Gradle如下图

Gradle下载地址：<https://services.gradle.org/distributions/>

![2019-06-19_105706]({{site.baseurl}}/assets/img/2019-06-19_105706.png)

#### 3、Gradle的配置

Gradle下载好了后不用安装，将其解压在合适目录后，与jdk相似设置环境变量：

- 新建系统环境变量`GRADLE_HOME`，值为安装目录，比如我的就是`D:\Program Files\gradle\gradle-4.9`

- 在环境变量path中新增`%GRADLE_HOME%\bin\`

- 在cmd中执行`gradle -v`，出现了如下所示的说明表示安装成功

  - ```
    Welcome to Gradle 4.9!
    
    Here are the highlights of this release:
     - Experimental APIs for creating and configuring tasks lazily
     - Pass arguments to JavaExec via CLI
     - Auxiliary publication dependency support for multi-project builds
     - Improved dependency insight report
    
    For more details see https://docs.gradle.org/4.9/release-notes.html
    
    
    ------------------------------------------------------------
    Gradle 4.9
    ------------------------------------------------------------
    
    Build time:   2018-07-16 08:14:03 UTC
    Revision:     efcf8c1cf533b03c70f394f270f46a174c738efc
    
    Kotlin DSL:   0.18.4
    Kotlin:       1.2.41
    Groovy:       2.4.12
    Ant:          Apache Ant(TM) version 1.9.11 compiled on March 23 2018
    JVM:          1.8.0_121 (Oracle Corporation 25.121-b13)
    OS:           Windows 10 10.0 amd64
    ```

- 配置Gradle本地仓库，Gradle和maven相似，都有本地目录（Gradle默认`C:\Users\username\.gradle`，maven默认`C:\Users\username\.m2`）。我们可以通过配置环境变量来指定我们自己的本地仓库。

  - 新增系统环境变量`GRADLE_USER_HOME`，值写为合适的本地路径即可，比如我的是`E:\RepoGradle`。

jdk和IDEA的安装我就不多说了，想必能开始阅读Spring的源码了，这两个的安装也驾轻就熟了吧。

以上系统、软件都安装好了就可以开始进行源码编译了

### 二、查看jdk版本

**jdk版本必须是1.8.0_121**！！！	**jdk版本必须是1.8.0_121**！！！	**jdk版本必须是1.8.0_121**！！！

重要的事情说三遍（PS：其他的jdk版本应该也行，我没有试过，有兴趣可以去网上看看哪些jdk版本行得通，但我一开始用的是**1.8.0_202**，失败了好几次(￣﹃￣)）。

如果版本不是**1.8.0_121**也没关系，我们可以在本地上安装多个版本的jdk。我主机上原本安装的是jdk1.8.0_202，接着又安装了jdk1.8.0_121。

#### 1、配置环境变量

首先去官网下载对应版本的jdk，然后一直下一步下一步安装成功。jdk在安装的时候完全没问题，关键在于环境变量的配置，我的环境变量配置如下图：

![2019-06-19_135731]({{site.baseurl}}/assets/img/2019-06-19_135731.png)

当我们想要转换不同版本的环境变量时，只需要修改变量`JAVA_HOME`的值即可。如果想要添加jdk版本也只需增加一个系统环境变量即可。**并且path变量中的值完全不用修改**。

上述步骤做完后打开`控制面板->程序->Java`，然后选择jdk版本，确定保存

![2019-06-19_141033]({{site.baseurl}}/assets/img/2019-06-19_141033.png)

上述步骤全部完成后可以试试`win+r, cmd`试试看Java版本是否成功改变，如果没有改变过也不用担心。

#### 2、删除与Java相关的运行程序

删除`C:\Windows\System32`目录下的与java相关的运行程序，例如 `java.exe，javax.exe，javac.exe`，如果没有可直接跳过这步

删除`C:\Program Files (x86)\Common Files\Oracle\Java\javapath`（这个我直接删除了javapath文件夹）和`C:\ProgramData\Oracle\Java\javapath`（这个我什么都没删）的javapath的快捷图标和Java相关的运行程序。这是因为path中配置了相关的引用。如果没有可以直接跳过这步。

#### 3、更新注册表对应的值

1. `win+r`，输入`regedit`运行注册表
2. 找到`HKEY_LOCAL_MACHINE\SOFTWARE\JavaSoft\Java Development Kit`
3. 修改`CurrentVersion`项的值为空
4. 找到`HKEY_LOCAL_MACHINE\SOFTWARE\JavaSoft\Java Runtime Environment`
5. 修改`CurrentVersion`项的值为空



接下来`win+r`，输入`cmd`运行命令行窗口，键入`java -version`便可以看到jdk版本成功改变

参考链接：<https://blog.csdn.net/programminging/article/details/80770294>



### 三、Spring源码编译

#### 1、导入工程

1）、从已经存在的工程中创建工程，选择从GitHub上下载的源码目录

![2019-06-19_143634]({{site.baseurl}}/assets/img/2019-06-19_143634.png)

2）、选择Gradle

![2019-06-19_143913]({{site.baseurl}}/assets/img/2019-06-19_143913.png)

3）、Gradle的一些设置

![2019-06-19_144036]({{site.baseurl}}/assets/img/2019-06-19_144036.png)

4）、点击finish后，可以看到右下角的running

![2019-06-19_144756]({{site.baseurl}}/assets/img/2019-06-19_144756.png)

5）、接着很大程度上能看到build失败，点击`Open File`，可以定位到spring-beans.gradle文件，注释掉定位的行即可

![2019-06-19_145240]({{site.baseurl}}/assets/img/2019-06-19_145240.png)

6）、点击右侧栏的Gradle，弹出右边框，点击刷新可以看到Spring的Gradle工程

![2019-06-19_145635]({{site.baseurl}}/assets/img/2019-06-19_145635.png)

#### 2、编译工程

##### 1）、spring-core

首先`Run 'Tests in java'`，会发现有一堆错误，找不到符号啊，找不到包啊（如果没有报错可以直接过。比如spring-bean和spring-context）

![2019-06-19_150036]({{site.baseurl}}/assets/img/2019-06-19_150036.png)

然后点击右侧Gradle中的`compileTestJava`，successful后再次执行上一步会发现执行成功。因为这一步将所需要的依赖包下载进工程。但还是会有一个小错误，这里应该是断言的问题，定位到指定行注释即可，对后面分析Spring源码没有问题。

![2019-06-19_150439]({{site.baseurl}}/assets/img/2019-06-19_150439.png)

![2019-06-19_150824]({{site.baseurl}}/assets/img/2019-06-19_150824.png)

![2019-06-19_151006]({{site.baseurl}}/assets/img/2019-06-19_151006.png)

到这里，spring-core成功编译

##### 2）、spring-bean、spring-context

以同样的方式编译spring-bean和spring-context	

![2019-06-19_151513]({{site.baseurl}}/assets/img/2019-06-19_151513.png)

![2019-06-19_151905]({{site.baseurl}}/assets/img/2019-06-19_151905.png)

##### 3）、spring-oxm

接着以同样的方式编译spring-oxm，会发现一堆包找不到

![2019-06-19_151156]({{site.baseurl}}/assets/img/2019-06-19_151156.png)

![2019-06-19_152258]({{site.baseurl}}/assets/img/2019-06-19_152258.png)

接下来点击右侧Gradle，在弹出测边框双击**genJaxb**和**genCastor**

![2019-06-19_152400]({{site.baseurl}}/assets/img/2019-06-19_152400.png)

执行完后再次运行`Run All Tests`，竟然发现还有23个错误（吐血三升━┳━　━┳━），这里我也不知道怎么搞下去了，希望能有大佬来教教俺。不过这些错误也不影响程序的运行(lll￢ω￢)，果然，代码就是一门玄学。

![2019-06-19_152645]({{site.baseurl}}/assets/img/2019-06-19_152645.png)



到这里，阅读Spring源码的环境就已经全部搭建完成了