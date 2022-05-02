## JVM 的主要组成部分及其作用

- 类加载器（ClassLoader）
- 运行时数据区（Runtime Data Area）
- 执行引擎（Execution Engine）
- 本地库接口（Native Interface）

组件的作用： 首先通过类加载器（ClassLoader）会加载类文件到内存，Class loader只管加载，只要符合文件结构就加载。运行时数据区（Runtime Data Area)是jvm的重点，我们所有所写的程序都被加载到这里，之后才开始运行。而字节码文件只是 JVM 的一套指令集规范，并不能直接交个底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来融合不同的语言为java所用,从而实现整个程序的功能。

## JVM 运行时数据区

- 程序计数器

  指向当前线程执行的字节码指令的地址（ 行号 ）。 这样做的**用处是多线程操作时， 挂起的线程在重新激活后能够知道上次执行的位置。**

- 虚拟机栈

  存储当前线程执行方法时所需要的数据，指令，返回地址。因此一个线程独享一块虚拟机栈 ，栈中内存的单位是栈帧。 每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法出口信息。

- 本地方法栈

  本地方法栈为虚拟机使用到的 Native 方法服务。

- 堆

  唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。 详见堆内存模型。

- 方法区

  用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。

  ![img](../图片/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZGQzYjE1YjNkODgyNmZhZWFlMjA2Mzk3NmZiOTkyMTM_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ.webp)

### 堆

堆内存模型：

[![image-20200227234850635](../图片/JVM.png)](https://github.com/lvminghui/Java-Notes/blob/master/docs/imgs/JVM.png)

**此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存。**

## 堆和栈的区别

1. 栈内存存储的是局部变量而堆内存存储的是实体；
2. 栈内存的更新速度要快于堆内存，因为局部变量的生命周期很短；
3. 栈内存存放的变量生命周期一旦结束就会被释放，而堆内存存放的实体会被垃圾回收机制不定时的回收。、

