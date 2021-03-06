# might_sleep的作用

[include/linux/kernel.h](https://github.com/torvalds/linux/blob/master/include/linux/kernel.h)中对`might_sleep`的注释如下

> this macro will print a stack trace if it is executed in an atomic context (spinlock, irq-handler, ...).
>
> This is a useful debugging help to be able to catch problems early and not be bitten later when the calling function happens to sleep when it is not supposed to.

正常编译时，`might_sleep`宏展开为`do { do { } while (0); } while (0)`，只有在`make menuconfig`时选择

```
Kernel Hacking  --->
  Lock Debugging (spinlocks, mutexes, etc..)  --->
    [*] Sleep inside atomic section checking
```

才能展开为`do { __might_sleep(__FILE, __LINE__, 0); do { } while (0); } while (0)`，`__might_sleep`会在atomic context中输出调试信息。因此`might_sleep`宏只能作为*annotation for functions that can sleep*，本身没有任何作用。

# 获取调用关系

## 使用egypt获取调用关系

首先安装[egypt](https://www.gson.org/egypt/)

```bash
curl https://www.gson.org/egypt/download/egypt-1.10.tar.gz | tar -x
cd egypt-1.10
perl Makefile.PL
make
make install
```

编译kernel时，在`make menuconfig`后修改Makefile，在Makefile中找到`# Make variables (CC, etc...)`并进行修改

```diff
@@ -348,7 +348,7 @@
 # Make variables (CC, etc...)
 AS     = $(CROSS_COMPILE)as
 LD     = $(CROSS_COMPILE)ld
-CC     = $(CROSS_COMPILE)gcc 
+CC     = $(CROSS_COMPILE)gcc -fdump-rtl-expand
 CPP        = $(CC) -E
 AR     = $(CROSS_COMPILE)ar
 NM     = $(CROSS_COMPILE)nm
```

编译完成后，在目标目录使用以下命令生成DOT文件

```bash
egypt `find . -name "*.expand"` --include-external > callgraph.dot
```

callgraph.dot包含了函数之间的调用关系。

## 使用gcc-python-plugin获取调用关系

在Makefile中找到`# Make variables (CC, etc...)`并进行修改

```diff
@@ -348,7 +348,7 @@
 # Make variables (CC, etc...)
 AS     = $(CROSS_COMPILE)as
 LD     = $(CROSS_COMPILE)ld
-CC     = $(CROSS_COMPILE)gcc
+CC     = $(CROSS_COMPILE)gcc -fplugin=~/gcc-python-plugin/python.so -fplugin-arg-python-script=~/rt-patch-analysis/developers/zw/callgraph/find_call.py
 CPP        = $(CC) -E
 AR     = $(CROSS_COMPILE)ar
 NM     = $(CROSS_COMPILE)nm
```

编译完成后在目标目录下可找到callgraph.txt，该文件内包含了函数之间的调用关系。

# 构造callgraph

首先使用`pip install networkx`安装[NetworkX](https://networkx.github.io/)包，在目标目录下运行build_graph.py以生成之后分析所需的文件。

# 静态分析

* LLVM
    - 需要给RT Linux打[LLVM Patch](http://llvm.linuxfoundation.org/index.php/Main_Page)
* [pycparser](https://github.com/eliben/pycparser)
* [GCC plugins](https://gcc.gnu.org/wiki/plugins)
    - [GCC Python plugin](https://gcc-python-plugin.readthedocs.io/en/latest/)

## pycparser

Linux源码必须先使用C预处理器处理后才能被正常解析，执行

```bash
find . -type f -name '*.c' -exec sh -c 'make O=../v4.11.5-rt1 "${0%.c}.i"' {} \;
```

## GCC Python plugin

首先获取并编译[gcc-python-plugin](https://github.com/davidmalcolm/gcc-python-plugin.git)

```bash
git clone https://github.com/davidmalcolm/gcc-python-plugin.git
cd gcc-python-plugin
git checkout tags/v0.15
make
```

在Kernel的Makefile中找到`# Make variables (CC, etc...)`并进行修改

```diff
@@ -348,7 +348,7 @@
 # Make variables (CC, etc...)
 AS     = $(CROSS_COMPILE)as
 LD     = $(CROSS_COMPILE)ld
-CC     = $(CROSS_COMPILE)gcc
+CC     = $(CROSS_COMPILE)gcc -fplugin=~/gcc-python-plugin/python.so -fplugin-arg-python-script=~/rt-patch-analysis/developers/zw/callgraph/check.py
 CPP        = $(CC) -E
 AR     = $(CROSS_COMPILE)ar
 NM     = $(CROSS_COMPILE)nm
```

编译内核，编译完成后检查log.txt文件。
