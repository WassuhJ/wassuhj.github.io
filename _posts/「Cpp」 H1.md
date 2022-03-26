# 「Cpp」 H1

* 理解库的概念，创建与调用库



## 库

### 概念

库是写好的现有的，成熟的，可以复用的代码。现实中每个程序都要依赖很多基础的底层库，不可能每个人的代码都从零开始，因此库的存在意义非同寻常。

本质上来说库是一种可执行代码的二进制形式，可以被操作系统载入内存执行。外部函数不进入安装包，只在运行时动态引用。



### 种类

库有两种：静态库和动态库，所谓静态、动态是指链接。

将一个程序编译成可执行程序的步骤：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190905143416528.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1bnh1ZXpoZW5n,size_16,color_FFFFFF,t_70)

链接： 把外部函数的代码（通常是后缀名为.lib和.a的文件），添加到可执行文件中



#### 命名方式

* linux
  * 静态库：.a
  * 动态库：.so (shared object)

* window
  * 静态库: .lib
  * 动态库: .dll

库是包含前缀lib以及后缀.a或者.so的一个整体，库名是去掉前缀和后缀剩下的部分



### 动态库和静态库的特点

* 静态库的特点
  * 使用静态库的时候，会将静态库的信息直接编译到可执行文件中
  * 优点：当静态库被删除，对可执行文件没有影响
  * 缺点：浪费内存空间。如果静态库被修改，可执行程序要重新编译

* 动态库的特点
  * 加载器在加载动态库时，操作系统会先检查动态库是否因为其它程序已经将这个动态库信息加载到了内存中。如果没有加载到内存中，操作系统会将动态库载入内存，并将它的引用计数设置为1；如果已经加载到内存，仅将动态库的引用计数加1。



### Linux 创建过程

* 静态库
  * 创建 .o 文件
  * `gcc ar -cr libxxxx.a *.o`

* 动态库
  * 创建 .o 文件，使用 `-fPIC` 选项： `-fPIC` 告诉 GCC 产生的代码不要包含对函数和变量具体内存位置的引用，这是因为现在还无法知道使用该代码的应用程序会将它连接到哪一段内存地址空间。
  * `gcc -shared -o libxxxx.so *.o`

`ldd` 可以查看一个可执行程序依赖的共享库：`ldd + 路径/可执行程序`



### 如何使用库

* 静态库
  * 放在系统目录/lib或者/usr/lib下：`gcc main.c -l库名`
  * 否则采用以下两种：
    * `gcc main.c -L 静态库路径 -l库名`
    * `gcc main.c 路径/整个静态库`  

* 动态库

  * 放在系统目录下执行方式为 `gcc main.c -l库名`

  * 否则采用以下两种：

    * `gcc main.c -L 动态库路径 -l库名`

      报错：`./a.out: error while loading shared libraries: libadd.so: cannot open shared object file: No such file or directory`

      解决方式：

      1、在 /etc/bash.bashrc 文件中添加 `export LD_LIBRARY_PATH=$LD_LRBRARY_PATH`。后 `source /etc/bash.bashrc`

      2、在 /etc/ld.so.conf.d 目录下创建一个以 .conf 为后缀的文件，在这个文件中添加上动态库的绝对路径，后执行 `sudo ldconfig`

      ldconfig命令的用途,主要是在默认搜寻目录(/lib和/usr/lib)以及动态库配置文件/etc/ld.so.conf内所列的目录下,搜索出可共享的动态链接库.

    * 如果第一种的方式都配置完之后出现错误：

      ```
      /usr/bin/ld: cannot find -laddsub
      collect2: ld returned 1 exit status
      ```

      这种错误说明编译器找不到需要链接的库，则执行 `gcc main.c 动态库路径/动态库`

