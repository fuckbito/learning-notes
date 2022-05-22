## Java概述

#### 1.JVM、JRE和JDK的关系

JVM:
Java Virtual Machine是Java虚拟机，Java程序需要运行在虚拟机上，不同的平台有自己的虚拟机，因此Java语言可以实现跨平台。

JRE:
Java Runtime Environment包括Java虚拟机和Java程序所需的核心类库等。核心类库主要是java.lang包：包含了运行Java程序必不可少的系统类，如基本数据类型、基本数学函数、字符串处理、线程、异常处理类等，系统缺省加载这个包

如果想要运行一个开发好的Java程序，计算机中只需要安装JRE即可。

JDK:
Java Development Kit是提供给Java开发人员使用的，其中包含了Java的开发工具，也包括了JRE。所以安装了JDK，就无需再单独安装JRE了。其中的开发工具：编译工具(javac.exe)，打包工具(jar.exe)等

![](C:\Users\HP\Desktop\笔记\Imag\JVM&JDK&JRE.png)



#### 2.JVM

##### 1.jvm

![img](https://camo.githubusercontent.com/131785c358b810e45abc6a71c98bc03bacbd2e54/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d31312f38393738363365653565636234643932623931313964303635663436383236322d6e65772d696d61676566373238376630622d633966302d346632322d396562342d3639363862626161356138322e706e67)

**JVM的内存结构**

- 类加载器：如果 **JVM** 想要执行这个 **.class** 文件，我们需要将其装进一个 **类加载器** 中，它就像一个搬运工一样，会把所有的 **.class** 文件全部搬进JVM里面来。

   [![img](https://camo.githubusercontent.com/24dd12441eb0ee31f0798c29f904114433e433e0/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d31312f38316631383133663337316334306666613163316636643738626334396564392d6e65772d696d61676532383331346563382d303636662d343531652d383337332d3435313739313764366266372e706e67)](https://camo.githubusercontent.com/24dd12441eb0ee31f0798c29f904114433e433e0/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d31312f38316631383133663337316334306666613163316636643738626334396564392d6e65772d696d61676532383331346563382d303636662d343531652d383337332d3435313739313764366266372e706e67)

- 方法区：存储已**被虚拟机加载的类元数据信息**(元空间)比如类信息，常量，静态变量，编译后代码···等，类加载器将 `.class` 文件搬过来就是先丢到这一块上。

- 堆：**存放对象实例**，比如对象实例，数组···等，它和方法区都同属于 **线程共享区域** 。也就是说它们都是 **线程不安全** 的，主要用于存放新创建的对象，几乎所有的对象实例都在这里分配内存。

- 栈： 这是我们的代码运行空间。我们编写的每一个方法都会放到 **栈** 里面运行。

- 虚拟机栈：虚拟机栈描述的是**Java方法执行的内存结构**：每个方法被执行的时候都会同时创建一个**栈帧**（Stack Frame）用于存储局部变量表、操作栈、动态链接、方法出口等信息

- 本地方法栈：本地方法栈则是为虚拟机使用到的**Native方法服务**。

- 程序计数器：当前线程所执行的字节码的**行号指示器**，主要就是完成一个加载工作，类似于一个指针一样的，指向下一行我们需要执行的代码。和栈一样，都是 **线程独享** 的，就是说每一个线程都会有自己对应的一块区域而不会存在并发和多线程的问题。



##### 3.什么情况下会发生栈内存溢出。

线程的栈深度大于虚拟机运行的最大深度，将抛出StackOverflowError异常，常常出现于递归的方法调用。

**思路：** 描述栈定义，再描述为什么会溢出，再说明一下相关配置参数，OK的话可以给面试官手写是一个栈溢出的demo。

**参考答案：**

- 栈是线程私有的，他的生命周期与线程相同，每个方法在执行的时候都会创建一个栈帧，用来存储局部变量表，操作数栈，动态链接，方法出口等信息。局部变量表又包含基本数据类型，对象引用类型
- 如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常，方法递归调用产生这种结果。
- 如果Java虚拟机栈可以动态扩展，并且扩展的动作已经尝试过，但是无法申请到足够的内存去完成扩展，或者在新建立线程的时候没有足够的内存去创建对应的虚拟机栈，那么Java虚拟机将抛出一个OutOfMemory 异常。(线程启动过多)
- 参数 -Xss 去调整JVM栈的大小

##### 2.垃圾回收

**简述Java垃圾回收机制** 

在java中，程序员是不需要显示的去释放一个对象的内存的，而是由虚拟机自行执行。**在JVM中，有一个垃圾回收线程，它是低优先级的，在正常情况下是不会执行的，只有在虚拟机空闲或者当前堆内存不足时，才会触发执行**，扫面那些没有被任何引用的对象，并将它们添加到要回收的集合中，进行回收。 

**GC是什么？为什么要GC** 

GC 是垃圾收集的意思（Gabage Collection）,内存处理是编程人员容易出现问题的地方，忘记或者错误的内存 

回收会导致程序或系统的不稳定甚至崩溃，Java 提供的 GC 功能可以自动监测对象是否超过作用域从而达到自动 

回收内存的目的，Java 语言没有提供释放已分配内存的显示操作方法。 

**垃圾回收的优点和原理。并考虑2种回收机制** 

Java语言最显著的特点就是引入了垃圾回收机制，它使java程序员在编写程序时不再考虑内存管理的问题。 

由于有这个垃圾回收机制，java中的对象不再有“作用域”的概念，只有引用的对象才有“作用域”。 

垃圾回收机制有效的防止了内存泄露，可以有效的使用可使用的内存。 

垃圾回收器通常作为一个单独的低级别的线程运行，在不可预知的情况下对内存堆中已经死亡的或很长时间没有用过的对象进行清除和回收。

程序员不能实时的对某个对象或所有对象调用垃圾回收器进行垃圾回收。 

垃圾回收有分代复制垃圾回收、标记垃圾回收、增量垃圾回收。 

**垃圾回收器的基本原理是什么？垃圾回收器可以马上回收内存吗？有什么办法主动通知虚拟机进行垃圾回收？** 

对于GC来说，当程序员创建对象时，GC就开始监控这个对象的地址、大小以及使用情况。  通常，GC采用有向图的方式记录和管理堆(heap)中的所有对象。通过这种方式确定哪些对象是"可达的"，哪些对象是"不可达的"。当GC确定一些对象为"不可达"时，GC就有责任回收这些内存空间。 

可以。程序员可以手动执行System.gc()，通知GC运行，但是Java语言规范并不保证GC一定会执行。 

**怎么判断对象是否可以被回收？** 

垃圾收集器在做垃圾回收的时候，首先需要判定的就是哪些内存是需要被回收的，哪些对象是「存活」的，是不可以被回收的；哪些对象已经「死掉」了，需要被回收。 

一般有两种方法来判断： 

引用计数器法：为每个对象创建一个引用计数，有对象引用时计数器 +1，引用被释放时计数 -1，当计数器为 0 时就可以被回收。它有一个缺点不能解决循环引用的问题； 

可达性分析[算法]()：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是可以被回收的。 

![image-20220322112815576](C:\Users\HP\Desktop\笔记\Imag\jvm垃圾回收.png)

## Java基础

#### 1.基本的数据类型

基本数据类型: **boolean，char，byte，short，int，long，float，double**。

**boolean**有两个常量值:**false**和**true**，它们全是小写字母，在计算机内用8位表示。Java是一种严格的类型语言，它不允许数值类型和布尔类型之间进行转换。

单个字符用**char**类型表示，一个**char**表示一个**Unicode**字符，其值用16位无符号数表示，范围为 0~2^16-1=65535。
**char**类型的常量值必须用一对单引括号**(’ ’)**括起来。



####  2.String比较中“==”与equals()区别？

**==**：如果是基本数据类型，进行的是数值比较，如果用于引用数据类型，比较的则是两个内存的地址数值；

**equals()**：是类所提供的一个比较方法，可以直接进行字符串内容的判断，**区分大小写**。



#### 3.构造方法

```java
class Person{
    private String name;
    private int id;
    public Person(String name,int id){
        this.name=name;
        this.id=id;
    }
}
```

主要目的：可以通过构造方法实现实例化对象中的属性初始化处理。相当于调用setter方法。

要求如下：

- 构造方法必须与类名保持一致；
- 构造方法不允许设置任何的返回值类型，即：没有返回值定义；
- 构造方法是在使用关键字new实例化对象的时候自动调用的。

结论：一个类至少存在有一个构造方法，永恒存在。若没有手动构造，则类存在一个默认无参构造方法。

**问题：为什么构造方法上不允许设置返回值类型？**

如果在构造方法上使用了void，那么此结构就与普通方法的结构完全相同了，这样编译器会认为此方法是一个普通方法。

**普通方法与构造方法最大的区别**：

构造方法是在类对象实例化的时候调用的，而普通方法是在类对象实例化产生之后调用的。

#### 4.覆写和重载

- **覆写（Override）是指子类对父类方法的一种重写，** 只能比父类抛出更少的异常，访问权限不能比父类的小，被覆写的方法不能是 private 的，否则只是在子类中重新定义了一个新方法。

- **重载（Overload）表示同一个类中可以有多个名称相同的方法，但这些方法的参数列表各不相同。**

- **构成重载的条件有哪些？** 参数类型不同、参数个数不同、参数顺序不同。

- 覆写原因：子类与父类一旦产生继承关系之后，实际上子类会继承父类中的全部定义，但是这里面也有可能出现不合适的场景，子类如果发现父类中设计不足并且需要保留父类中的方法或者属性名称的情况下就会发生覆写。

- 方法的覆写：当子类定义了与父类方法名称相同，参数类型及个数完全相同（和父类方法一模一样）的时候，就称为方法的覆写。

```java
class Channel{
    public void connect(){
        System.out.println("父类进行资源的连接。");
    }
}
class DatabaseChannel extends Channel{
    @Override //明确表达该方法是一个覆写的方法，帮助检查出程序错误
    public void connect(){
        System.out.println("子类进行资源的连接。");
    }
}

```



#### 5.多态

本质上多态分两种：

1. 编译时多态（又称静态多态）
2. 运行时多态（又称动态多态）

重载（overload）就是编译时多态的一个例子，编译时多态在编译时就已经确定，运行时运行的时候调用的是确定的方法。

我们通常所说的多态指的都是运行时多态，也就是编译时不确定究竟调用哪个具体方法，一直延迟到运行时才能确定。这也是为什么有时候多态方法又被称为延迟方法的原因。



#### 6.向上转型、向下转型

- **向上转型** : 通过子类对象(小范围)实例化父类对象(大范围),这种属于自动转换，向上转型时,父类只能调用父类方法或者子类覆写后的方法,而子类中的单独方法则是无法调用的。无法通过引用变量调用子类特有的方法，即便是父类中有同名抽象方法也不行。

- **向下转型** : 通过父类对象(大范围)实例化子类对象(小范围),这种属于强制转换



#### 7.泛型

泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

https://blog.csdn.net/s10461/article/details/53941091



#### 8.抽象类

抽象方法是一种特殊的方法：它只有声明，而没有具体的实现。抽象方法的声明格式为：

```
abstract void fun();
```

抽象方法必须用**abstract关键字**进行修饰。如果一个类含有抽象方法，则称这个类为抽象类，抽象类必须在类前用abstract关键字修饰。**因为抽象类中含有无具体实现的方法，所以不能用抽象类创建对象**

抽象类基本定义：抽象类的主要作用在于对子类中覆写方法进行约定，在抽象类里面可以去定义一些抽象方法以实现这样的约定，抽象方法指的是使用了abstract关键字定义的并且没有提供方法体的方法，而抽象方法所在的类必须为抽象类，抽象类必须使用abstract关键字来定义（在普通类的基础上追加抽象方法就是抽象类）

包含抽象方法的类称为抽象类，但并不意味着抽象类中只能有抽象方法，它和普通类一样，同样可以拥有成员变量和普通的成员方法。注意，抽象类和普通类的主要有三点区别：

1、抽象方法必须为public或者protected（因为如果为private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认public。

2、抽象类不能用来创建对象；

3、如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为abstract类。在其他方面，抽象类和普通的类并没有区别。

**注：**

1. 抽象类中允许没有抽象方法，但即便没有抽象方法，也无法直接使用关键字new实例化抽象类对象。（抽象类的对象实例化可以利用对象多态性通过子类向上转型的方式完成。）
2. 抽象类中可以提供有static方法，并且该方法不受到抽象类对象的局限。
3. 在定义抽象类的的时候绝对不能够使用final关键字来进行定义，因为抽象类必须有子类，而final定义的类是不能够有子类

#### 9.接口

接口，用**interface**关键字修饰。在Java中，定一个接口的形式如下：

```
[ public ]  interface  InterfaceName { }
```

接口中可以含有变量和方法。但是要注意，**接口中的变量会被隐式地指定为public static final变量（并且只能是public static final变量，用private修饰会报编译错误）**，而方法会被隐式地指定为public abstract方法且只能是public abstract方法（用其他关键字，比如private、protected、static、 final等修饰会报编译错误），并且接口中所有的方法不能有具体的实现，也就是说，接口中的方法必须都是抽象方法。从这里可以隐约看出接口和抽象类的区别，接口是一种极度抽象的类型，它比抽象类更加“抽象”，并且一般情况下不在接口中定义变量。

要让一个类遵循某组特地的接口需要使用implements关键字，具体格式如下：

```
class  ClassName  implements  Interface1,Interface2,[....]{}
```

可以看出，允许一个类遵循多个特定的接口。如果一个非抽象类遵循了某个接口，就必须实现该接口中的所有方法。对于遵循某个接口的抽象类，可以不实现该接口中的抽象方法。

**抽象类与接口的区别：**

| No.  | 区别       | 抽象类                                               | 接口                                           |
| ---- | ---------- | ---------------------------------------------------- | ---------------------------------------------- |
| 1    | 定义关键字 | abstract class 抽象类名称{}                          | interface 接口名称{}                           |
| 2    | 组成       | 构造、普通方法、静态方法、全局常量、成员、static方法 | 抽象方法、全局变量                             |
| 3    | 权限       | 可以使用各种权限定义                                 | 只能够使用public                               |
| 4    | 子类使用   | 子类通过extends关键字可以继承一个抽象类              | 子类使用implements关键字可以实现多个接口       |
| 5    | 两者关系   | 抽象类可以实现若干个接口                             | 接口不允许继承抽象类，但是允许继承多个父接口。 |

- 1.抽象类可以有构造方法（**实例化子类的时候，就会初始化父类，限制子类的构造行为**），接口中不能有构造方法。
- 2.抽象类中可以有普通成员变量，可以包含非抽象的普通方法；接口中没有普通成员变量。所有方法必须都是抽象的，不能有非抽象的普通方法。
- 3.抽象类中的抽象方法的访问类型可以是public，protected和（默认类型,虽然eclipse下不报错，但应该也不行），但接口中的抽象方法只能是public类型的，并且默认即为public abstract类型。
- 4.抽象类中可以包含静态方法，接口中不能包含静态方法。
- 5.抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是public static final类型，并且默认即为public static final类型。
- 6.一个类只能继承一个抽象类，而一个类却可以实现多个接口。



#### 10.String常用方法

| SN   |                           方法描述                           |
| ---- | :----------------------------------------------------------: |
| 1    | [char charAt(int index)](https://www.runoob.com/java/java-string-charat.html) 返回指定索引处的 char 值。 |
| 2    | [int compareTo(Object o)](https://www.runoob.com/java/java-string-compareto.html) 把这个字符串和另一个对象比较。 |
| 3    | [int compareTo(String anotherString)](https://www.runoob.com/java/java-string-compareto.html) 按字典顺序比较两个字符串。 |
| 4    | [int compareToIgnoreCase(String str)](https://www.runoob.com/java/java-string-comparetoignorecase.html) 按字典顺序比较两个字符串，不考虑大小写。 |
| 5    | [String concat(String str)](https://www.runoob.com/java/java-string-concat.html) 将指定字符串连接到此字符串的结尾。 |
| 6    | [boolean contentEquals(StringBuffer sb)](https://www.runoob.com/java/java-string-contentequals.html) 当且仅当字符串与指定的StringBuffer有相同顺序的字符时候返回真。 |
| 7    | [static String copyValueOf(char[] data)](https://www.runoob.com/java/java-string-copyvalueof.html) 返回指定数组中表示该字符序列的 String。 |
| 8    | [static String copyValueOf(char[] data, int offset, int count)](https://www.runoob.com/java/java-string-copyvalueof.html) 返回指定数组中表示该字符序列的 String。 |
| 9    | [boolean endsWith(String suffix)](https://www.runoob.com/java/java-string-endswith.html) 测试此字符串是否以指定的后缀结束。 |
| 10   | [boolean equals(Object anObject)](https://www.runoob.com/java/java-string-equals.html) 将此字符串与指定的对象比较。 |
| 11   | [boolean equalsIgnoreCase(String anotherString)](https://www.runoob.com/java/java-string-equalsignorecase.html) 将此 String 与另一个 String 比较，不考虑大小写。 |
| 12   | [byte[] getBytes()](https://www.runoob.com/java/java-string-getbytes.html) 使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
| 13   | [byte[] getBytes(String charsetName)](https://www.runoob.com/java/java-string-getbytes.html) 使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
| 14   | [void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)](https://www.runoob.com/java/java-string-getchars.html) 将字符从此字符串复制到目标字符数组。 |
| 15   | [int hashCode()](https://www.runoob.com/java/java-string-hashcode.html) 返回此字符串的哈希码。 |
| 16   | [int indexOf(int ch)](https://www.runoob.com/java/java-string-indexof.html) 返回指定字符在此字符串中第一次出现处的索引。 |
| 17   | [int indexOf(int ch, int fromIndex)](https://www.runoob.com/java/java-string-indexof.html) 返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索。 |
| 18   | [int indexOf(String str)](https://www.runoob.com/java/java-string-indexof.html) 返回指定子字符串在此字符串中第一次出现处的索引。 |
| 19   | [int indexOf(String str, int fromIndex)](https://www.runoob.com/java/java-string-indexof.html) 返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始。 |
| 20   | [String intern()](https://www.runoob.com/java/java-string-intern.html) 返回字符串对象的规范化表示形式。 |
| 21   | [int lastIndexOf(int ch)](https://www.runoob.com/java/java-string-lastindexof.html) 返回指定字符在此字符串中最后一次出现处的索引。 |
| 22   | [int lastIndexOf(int ch, int fromIndex)](https://www.runoob.com/java/java-string-lastindexof.html) 返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索。 |
| 23   | [int lastIndexOf(String str)](https://www.runoob.com/java/java-string-lastindexof.html) 返回指定子字符串在此字符串中最右边出现处的索引。 |
| 24   | [int lastIndexOf(String str, int fromIndex)](https://www.runoob.com/java/java-string-lastindexof.html) 返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索。 |
| 25   | [int length()](https://www.runoob.com/java/java-string-length.html) 返回此字符串的长度。 |
| 26   | [boolean matches(String regex)](https://www.runoob.com/java/java-string-matches.html) 告知此字符串是否匹配给定的正则表达式。 |
| 27   | [boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)](https://www.runoob.com/java/java-string-regionmatches.html) 测试两个字符串区域是否相等。 |
| 28   | [boolean regionMatches(int toffset, String other, int ooffset, int len)](https://www.runoob.com/java/java-string-regionmatches.html) 测试两个字符串区域是否相等。 |
| 29   | [String replace(char oldChar, char newChar)](https://www.runoob.com/java/java-string-replace.html) 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 |
| 30   | [String replaceAll(String regex, String replacement)](https://www.runoob.com/java/java-string-replaceall.html) 使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 |
| 31   | [String replaceFirst(String regex, String replacement)](https://www.runoob.com/java/java-string-replacefirst.html) 使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。 |
| 32   | [String[] split(String regex)](https://www.runoob.com/java/java-string-split.html) 根据给定正则表达式的匹配拆分此字符串。 |
| 33   | [String[] split(String regex, int limit)](https://www.runoob.com/java/java-string-split.html) 根据匹配给定的正则表达式来拆分此字符串。 |
| 34   | [boolean startsWith(String prefix)](https://www.runoob.com/java/java-string-startswith.html) 测试此字符串是否以指定的前缀开始。 |
| 35   | [boolean startsWith(String prefix, int toffset)](https://www.runoob.com/java/java-string-startswith.html) 测试此字符串从指定索引开始的子字符串是否以指定前缀开始。 |
| 36   | [CharSequence subSequence(int beginIndex, int endIndex)](https://www.runoob.com/java/java-string-subsequence.html) 返回一个新的字符序列，它是此序列的一个子序列。 |
| 37   | [String substring(int beginIndex)](https://www.runoob.com/java/java-string-substring.html) 返回一个新的字符串，它是此字符串的一个子字符串。 |
| 38   | [String substring(int beginIndex, int endIndex)](https://www.runoob.com/java/java-string-substring.html) 返回一个新字符串，它是此字符串的一个子字符串。 |
| 39   | [char[] toCharArray()](https://www.runoob.com/java/java-string-tochararray.html) 将此字符串转换为一个新的字符数组。 |
| 40   | String toLowerCase() 使用默认语言环境的规则将此 String 中的所有字符都转换为小写。 |
| 41   | [String toLowerCase(Locale locale)](https://www.runoob.com/java/java-string-tolowercase.html) 使用给定 Locale 的规则将此 String 中的所有字符都转换为小写。 |
| 42   | [String toString()](https://www.runoob.com/java/java-string-tostring.html) 返回此对象本身（它已经是一个字符串！）。 |
| 43   | [String toUpperCase()](https://www.runoob.com/java/java-string-touppercase.html) 使用默认语言环境的规则将此 String 中的所有字符都转换为大写。 |
| 44   | [String toUpperCase(Locale locale)](https://www.runoob.com/java/java-string-touppercase.html) 使用给定 Locale 的规则将此 String 中的所有字符都转换为大写。 |
| 45   | [String trim()](https://www.runoob.com/java/java-string-trim.html) 返回字符串的副本，忽略前导空白和尾部空白。 |
| 46   | [static String valueOf(primitive data type x)](https://www.runoob.com/java/java-string-valueof.html) 返回给定data type类型x参数的字符串表示形式。 |
| 47   | [contains(CharSequence chars)](https://www.runoob.com/java/java-string-contains.html) 判断是否包含指定的字符系列。 |
| 48   | [isEmpty()](https://www.runoob.com/java/java-string-isempty.html) 判断字符串是否为空。 |

#### 11.final

> https://www.cnblogs.com/dolphin0520/p/3736238.html

在Java中，final关键字可以用来修饰类、方法和变量（包括成员变量和局部变量）。

**1.修饰类**

当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。

**2.修饰方法**

使用final方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。只有在想明确禁止 该方法在子类中被覆盖的情况下才将方法设置为final的。

**3.修饰变量**

对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象，但是它指向的对象的内容是可变的。。

## Java 进阶

### 串行，并行、并发

串行是指多个任务时，各个任务按顺序执行，完成一个之后才能进行下一个。

并行指的是多个任务可以同时执行，两个任务同一时刻互不干扰的同时执行。

并发当有多个线程在操作时,如果系统只有一个CPU,则它根本不可能真正同时进行一个以上的线程，它只能把CPU运行时间划分成若干个时间段,再将时间 段分配给各个线程执行，在一个时间段的线程代码运行时，其它线程处于挂起状。.这种方式我们称之为并发

异步和同步：异步和同步是相对的，同步就是顺序执行，执行完一个再执行下一个，需要等待、协调运行。异步就是彼此独立,在等待某事件的过程中继续做自己的事，不需要等待这一事件完成后再工作。

### 线程和进程

#### 1.进程和线程

**进程是操作系统中进行保护和资源分配的基本单位**，操作系统分配资源以进程为基本单位。而线程是进程的组成部分，它代表了一条顺序的执行流。

区别：进程是系统进行资源分配的一个独立单位，线程是进程的一个实体,是[CPU](https://so.csdn.net/so/search?q=CPU&spm=1001.2101.3001.7020)调度和分派的基本单位,它是比进程更小的能独立运行的基本单位，一个进程可以包括多个线程

线程的五大状态：

- 新建 创建线程还没有start
- 就绪 start但是还没有获取CPU时间片
- 运行 正在执行
- 阻塞 比如sleep等等状态
- 死亡 正常退出或者让发生异常

**线程调度:**

* 分时调度
  所有线程轮流使用CPU的使用权，平均分配每个线程占用CPU的时间。
* 抢占式调度
  优先让优先级高的线程使用CPU,如果线程的优先级相同，那么会随机选择一个(线程随机性), Java使用的为抢占式调度。

大部分操作系统都支持多进程并行运行，现在的操作系统几乎都支持同时运行多个程序。比如:现在我们上课-边使用编辑器，-边使用录屏软件,同时还开着画图板, dos窗口等软件。此时，这些程序是在同时运行，”感觉这些软件好像在同一时刻运行着“。实际上，CPU(中央处理器)使用抢占式调度模式在多个线程间进行着高速的切换。对于CPU的一个核而言，某个时刻，只能执行一个线程， 而CPU的在多个线程间切换速度相对我们的感觉要快，看上去就是在同一时刻运行。其实，多线程程序并不能提高程序的运行速度，但能够提高程序运行效率,让CPU的使用率更高。

#### 2.创建线程

**方法一：继承Thread类，重写run方法，使用start()方法来启动多线程。【Java里面提供有java.lang.Thread类，一个类只要继承了此类就表示这个类为线程的主体，同时还需要覆写Thread类中提供的一个run()方法，而这个方法就属于线程的主方法。】**

```java
import java.lang.Thread;

class MyThread extends Thread{

    public String name;
    public MyThread(String name){
        this.name=name;
    }
    @Override
    public void run(){
        for (int i=0;i<10;i++){
            System.out.println(this.name+"运行：i="+i);
        }
    }
}
```

```
public class ThreadDemo {
    public static void main(String[] args) {
        MyThread myThread_1=new MyThread("线程1");myThread_1.start();
        MyThread myThread_2=new MyThread("线程2");myThread_2.start();
        MyThread myThread_3=new MyThread("线程3");myThread_3.start();
    }
}
/**
注：虽然调用了start()方法，但最终执行的是run()方法，并且所有的线程对象都是交替执行的。线程的启动使用的是start()，但是启动的时候将进入到一种就绪状态，现在并没有执行，要等待资源调度。
**/
```

**方法二：**[Runnable接口实现多线程](https://erxionglsx.github.io/#/./docs/Java进阶?id=_3runnable接口实现多线程)

1.定义Runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
2.创建Runnable实现类的实例，并以此实例作为Thread的target来创建Thread对象,该Thread对象才提真正的线程对象。
3.调用线程对象的start()方法来启动线程。

```java
class myThread implements Runnable{
    private String title;
    public  myThread(String title) {
        this.title = title;
    }
      @Override
      public void run() {
          for (int i = 0; i < 10; i++) {
              System.out.println(this.title + "运行：i="+i);
        }
      }
}

public class RunnableDemo {
    public static void main(String[] args) {
          Thread threadA = new Thread(new myThread("线程对象A"));
          Thread threadB = new Thread(new myThread("线程对象B"));
          Thread threadC = new Thread(new myThread("线程对象C"));
          threadA.start();//启动多线程
          threadB.start();//启动多线程
          threadC.start();//启动多线程
    }
}
```

#### 3.线程常用方法：

##### [1.线程的命名与取得](https://erxionglsx.github.io/#/./docs/Java进阶?id=_1线程的命名与取得)

多线程的运行状态不确定，为了获取到一些需要使用的操作，所以线程的名字很重要，在Thread类之中就提供有线程名称。

- 构造方法：public Thread(Runnable target,String name); //
- 设置名字：public final void setName(String name);
- 取得名字：public final String getName();

```java
class MyThread implements Runnable{
      @Override
      public void run() {
        System.out.println(Thread.currentThread().getName());
      }
}

public class ThreadDemo {
    public static void main(String[] args) {
        MyThread mt = new MyThread();
        new Thread(mt,"线程C").start();
        new Thread(mt).start();
        new Thread(mt,"线程D").start();
    }
}
/*线程C
Thread-1
线程D*/
```

每当使用java命令执行程序的时候就表示启动了一个JVM的进程，一台电脑上可以同时启动若干个JVM进程，JVM进程都会有各自的线程。

在任何的开发之中，主线程可以创建若干个子线程，**创建子线程的目的是可以将一些复杂的逻辑交给子线程处理。主线程负责处理整体流程，而子线程负责处理耗时操作。**

```java
public static void main(String[] args) {
        System.out.println("1.执行操作任务一");
        new Thread(()->{ //子线程
            int temp = 0;
            for(int x=0;x<Integer.MAX_VALUE;x++) {
                temp += x;
            }
        }).start();
        System.out.println("2.执行操作任务二");
        System.out.println("n.执行操作任务N");
}
```



线程的命名：

public Thread(Runnable target,String name);
使用：new Thread(Runnable对象的引用，“线程名”).start();
public final synchronized void setName(String name);
使用：Thread.currentThread.setName(“线程名”)

线程信息的取得

public static native Thread currentThread();
使用：Thread.currentThread();//取得当前线程的所有信息
public final String getName();
使用：Thread.currentThread.getName();//取得当前线程的对象

##### [2.线程的优先级](https://erxionglsx.github.io/#/./docs/Java进阶?id=_6线程的优先级)

Java线程有优先级，我们可以通过传递参数给线程的**setPriority()** 来设置线程的优先级别
调整线程优先级：优先级高的线程会获得较多的运行机会。**优先级只能反映线程的中或者是紧急程度,不能决定是否一定先执行**
Java线程的优先级用整数表示，取值范围是1~10, Thread类有以下三个静态常量:

```java
static int MAX PRIORITY
			//线程可以具有的最高优先级，取值为10.
static int MIN PRIORITY
			//线程可以具有的最低优先级，取值为1.
static int NORM. PRIORITY
			//分配给线程的默认优先级，取值为5.
```

从理论上来说，线程的优先级越高越有可能先执行(越有可能先抢占到资源)。

- 设置优先级：public final void setPriority(int newPriority);
- 获取优先级：public final int getPriority()。

在进行优先级定义的时候都是通过int型的数字来完成的，而对于此数字的选择在Thread类里就定义有三个常量

- 最高优先级：public static final int MAX_PRIORITY;
- 中等优先级：public static final int NORM_PRIORITY;
- 最低优先级：public static final int MIN_PRIORITY;

```java
public static void main(String[] args) {
        Runnable run = ()->{
            for (int x = 0; x < 10; x++) {
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName()+"执行。");
            }
        };
        Thread threadA = new Thread(run,"线程对象A" );
        Thread threadB = new Thread(run,"线程对象B" );
        Thread threadC = new Thread(run,"线程对象C" );
        threadA.setPriority(Thread.MIN_PRIORITY);
        threadA.setPriority(Thread.MIN_PRIORITY);
        threadA.setPriority(Thread.MAX_PRIORITY);
        threadA.start();
        threadB.start();
        threadC.start();
}Copy to clipboardErrorCopied
```

主线程是一个中等优先级，而默认创建的线程也是中等优先级，为5。

##### 4.线程休眠

如果希望某一个线程可以暂缓执行，那么就可以使用休眠的处理，在Thread类之中定义：

- 休眠：public static void sleep(long millis) throws InterruptedException;
- 休眠：public static void sleep(long millis,int nanos) throws InterruptedException;

在进行休眠的时候可能会产生中断异常"InterruptedException",中断异常属于Exception的子类，所有该子类必须进行处理。

```java
public static void main(String[] args) {
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName()+"i="+i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        },"线程对象").start();
    }Copy to clipboardErrorCopied
```

注：

* 休眠的主要特点是可以自动实现线程的唤醒，以继续进行后续的处理。但如果你有多个线程对象，休眠也是有先后顺序的。

- Thread.sleep(long millis); 表示**当前线程**阻塞的毫秒数。
- 使用sleep()方法线程进入阻塞状态，时间到达后线程进入就绪状态。
- 每个对象都有一个锁，**sleep()不会释放锁。**

##### [5.线程的礼让](https://erxionglsx.github.io/#/./docs/Java进阶?id=_5线程的礼让)

线程的礼让指的是先将资源让出去让别的线程先执行。

- 礼让：public static void yield()

注：

- yield(); 让当前线程暂停，但不阻塞。
- 让当前线程从运行状态转为就绪状态，重新竞争CPU。
- **让CPU重新调度，礼让不一定成功！**

```java
public class ThreadDemo04 {
    static class YieldThread implements Runnable {
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName() + "： 开始启动");
            // 线程礼让
            Thread.yield();
            System.out.println(Thread.currentThread().getName() + "： 结束关机");
        }
    }
    
    public static void main(String[] args) {
        YieldThread yield = new YieldThread();
        new Thread(yield, "线程A").start();
        new Thread(yield, "线程B").start();
        /* A礼让成功，B礼让失败 
         * 线程A： 开始启动   
         * 线程B： 开始启动   
         * 线程B： 结束关机   
         * 线程A： 结束关机   
         */
    }
}
```


**sleep()和yield()的区别:**

* sleep()使当前线程进入停滞状态，所以执行sleep)的线程在指定的时间内肯定不会被执行;
* yield()只是使当前线程重新回到可执行状态，所以执行yield()的线程有可能在进入到可执行状态后马上又被执行。
  sleep方法使当前运行中的线程睡眼一段时间， 进入不可运行状态，这段时间的长短是由程序设定的，yield 方法使当前线程让出CPU占有权，但让出的时间是不可设定的。实际上, yield()方法对应了如下操作:先检测当前是否有相同优先级的线程处于同可运行状态，如有，则把CPU的占有权交给此线程，否则， 继续运行原来的线程。所以yield()方法称为“退让”，它把运行机会让给了同等优先级的其他线程。另外，sleep 方法允许较低优先级的线程获得运行机会，但yield()方法执行时，当前线程仍处在可运行状态，所以，不可能让出较低优先级的线程些时获得CPU占有权。在一个运行系统中，如果较高优先级的线程没有调用sleep方法，又没有受到I\O阻塞，那么,较低优先级线程只能等待所有较高优先级的线程运行结束，才有机会运行。

##### 6.线程的合并-Join

![image-20220317165651804](C:\Users\HP\Desktop\笔记\Imag\join.png)

线程的合并

* Thread中，join()方法的作用是调用线程等待该线程完成后，才能继续往下运行。
* join是Thread类的一个方法，启动线程后直接调用，即join()的作用是: “ 等待该线程终止”, 这里需要理解的就是该线程是指的主线程等待子线程的终止。也就是在子线程调用了join()方法后面的代码，只有等到子线程结束了才能执行。
* 为什么要用join()方法:
  在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束,但是如果主线程处理完其他的事务后，需要用到子线程的处理结果，也就是主线程需要等待子线程执行完成之后再结束，这个时候就要用到join()方法了。

```java
package com.demo.thread;

public class ThreadDemo05 {
    static class JoinThread implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 1000; i++) {
                System.out.println("join线程正在运行" + (i++));
            }
        }
    }
    public static void main(String[] args) {
        JoinThread join = new JoinThread();
        Thread t = new Thread(join);
        t.start();
        for (int i = 0; i < 1000; i++) {
            if (i == 200) {
                try {
                    t.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("main线程正在运行" + i);
        }
    }
}
```

##### 7.守护线程

- Java线程分为用户线程和守护线程

  - 用户线程：JVM必须等用户线程全部结束才能结束。eg. main线程

  - 守护线程：JVM不用等守护线程结束，也可以结束。eg.GC线程

- 设置守护线程的方法
  - Thread.setDaemon(true); // 表示将该线程设置位守护线程
  - Thread.setDaemon(false); // 用户线程，默认值位false；

- 注意：
  - **设置守护现在要在线程启动之前（在调用start()之前），要不然会报错IllegalThreadStateException异常**

守护线程.setDaemon(true):设置守护线程
线程有两类:用户线程(前台线程)、守护线程(后台线程)
**如果程序中所有前台线程都执行完毕了，后台线程会自动结束**
垃圾回收器线程属于守护线程。

```java
class DeamonThread extends Thread{
    @override 
    public void run() {
        for(inti=0;i<100;1++){
            System. out .printLn("守护线程打印: "+i);
        }


        try {
            Thread.sleep( millis: 2000);
        } catch (InterruptedException e) {
            e. printStackTrace();
        }
    }
}
public class TestDeamon {
    public static void main(String[] args) {
        //启动一个守护线程
        DeamonThread deamonThread = new DeamonThread();
        deamonThread.setDaenon(true);//就说明我这个是守护线程
        deamonThread. start(); 
        for(inti=0;i<20;i++){
            System. out .printLn("主线程打印: "+i);
        }
    }
}
```



#### 4.线程的生命周期



![image-20220317172813917](C:\Users\HP\Desktop\笔记\Imag\线程生命周期.png)

当线程被创建并启动以后，它既不是一启动就进入了执行状态，也不是一直处于执行状态。

**1.新建状态(New)**。
当线程对象对创建后，即进入了新建状态，如: Thread t =new MyThread();

**2.就绪状态(Runnable)**
当调用线程对象的start()方法(t.start();) ，线程即进入就绪状态。处于就绪状态的线程，只是说明此线程已经做好了准备，随时等待CPU调度执行,并不是说执行了t. start()此线程立即就会执行;

**3.运行状态(Running)**
当CPU开始调度处于就绪状态的线程时，此时线程才得以真正执行，即进入到运行状态。注：就绪状态是进入到运行状态的唯一 入口，也就是说，线程要想进入运行状态执行，首先必须处于就绪状态中；

**4.阻塞状态(Blocked)**
处于运行状态中的线程由于某种原因，暂时放弃对CPU的使用权，停止执行，此时进入阻塞状态，直到其进入到就绪状态,才有机会再次被CPU调用以进入到运行状态。根据阻塞产生的原因不同，阻塞状态又可以分为三种:

* 等待阻塞 --运行状态中的线程执行wait()方法，使本线程进入到等待阻塞状态;
* 同步阻塞 --线程在获取synchronized同步锁失败(因为锁被其它线程所占用),它会进入同步阻塞状态;
* 其他阻塞 --通过调用线程的leep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、 join()等待线程终止或者超时、或者/O处理完毕时，线程重新转入就绪状态。

```Java
/*
注：阻塞的情况分三种： 
等待阻塞(o.wait->等待对列)：运行(running)的线程执行 o.wait()方法，JVM 会把该线程放入等待队列(waitting queue) 中。

同步阻塞(lock->锁池) ：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则 JVM 会把该线 程放入锁池(lock pool)中。 

其他阻塞(sleep/join)：运行(running)的线程执行，Thread.sleep(long ms)或t.join()方法，或者发出了 I/O 请求时，JVM 会把该线程置为阻塞状态。当 sleep()状态超时、join()等待线程终止或者超时、或者 I/O 处理完毕时，线程重新转入可运行(runnable)状态。
*/
```

**5.死亡状态(Dead)**
线程执行完了或者因异常退出了run(方法，该线程结束生命周期。

*正常结束* 

1. run()或 call()方法执行完成，线程正常结束。 

*异常结束* 

2. 线程抛出一个未捕获的 Exception 或 Error。

*调用 stop* 

3. 直接调用该线程的 stop()方法来结束该线程----该方法通常容易导致死锁，不推荐使用。



#### 5.终止线程的方法

终止线程 4 种方式 

1. **正常运行结束 程序运行结束，线程自动结束。**

2. **使用退出标志退出线程**。一般 run()方法执行完，线程就会正常结束，然而，常常有些线程是伺服线程。它们需要长时间的 运行，只有在外部某些条件满足的情况下，才能关闭这些线程。使用一个变量来控制循环，例如： 最直接的方法就是设一个 boolean 类型的标志，并通过设置这个标志为 true 或 false 来控制 while 循环是否退出，代码示例：

   ```java
   public { ThreadSafe extends Thread 
       public volatile boolean exit = false; 
       public void run() { 
           while (!exit){
               //do something
           }
       } 
   }
   ```

   定义了一个退出标志 exit，当 exit为true时，while循环退出，exit 的默认值为false。在定义exit 时，使用了一个 Java 关键字 volatile，这个关键字的目的是使 exit 同步，也就是说在同一时刻只 能由一个线程来修改 exit 的值。

3. **Interrupt方法结束线程(两种方式)**

   * 1、线程处于阻塞状态：如使用了sleep；同步锁的 wait；socket中的receiver、accept等方法时，会使线程处于阻塞状态。当调用线程的interrupt()方法时，会抛出 InterruptException 异常。 阻塞中的那个方法抛出这个异常，通过代码捕获该异常，然后break跳出循环状态，从而让 我们有机会结束这个线程的执行。通常很多人认为只要调用 interrupt 方法线程就会结束，实际上是错的， 一定要先捕获 InterruptedException 异常之后通过 break 来跳出循环，才能正常结束 run 方法。
   * 2、线程未处于阻塞状态：使用 isInterrupted()判断线程的中断标志来退出循环。当使用 interrupt()方法时，中断标志就会置 true，和使用自定义的标志来控制循环是一样的道理。

   ```java
   public class ThreadSafe extends Thread {
       public void run() { 
           while (!isInterrupted()){ //非阻塞过程中通过判断中断标志来退出
               try{
                   Thread.sleep(5*1000);//阻塞过程捕获中断异常来退出
               }catch(InterruptedException e){
                   e.printStackTrace();
                   break;//捕获到异常之后，执行 break 跳出循环
               }
           }
       } 
   }
   ```

   

4. **stop 方法终止线程（线程不安全）**

   程序中可以直接使用 thread.stop()来强行终止线程，但是 stop 方法是很危险的，就象突然关闭计算机电源，而不是按正常程序关机一样，可能会产生不可预料的结果，不安全主要是： thread.stop()调用之后，创建子线程的线程就会抛出 ThreadDeatherror 的错误，并且会释放子线程所持有的所有锁。一般任何进行加锁的代码块，都是为了保护数据的一致性，如果在调用 thread.stop()后导致了该线程所持有的所有锁的突然释放(不可控制)，那么被保护数据就有可能呈现不一致性，其他线程在使用这些被破坏的数据时，有可能导致一些很奇怪的应用程序错误。因此，并不推荐使用 stop 方法来终止线程。



#### 6.线程安全/线程同步

##### 6.1 什么是线程安全

就是**线程同步**的意思，就是当一个程序对一个线程安全的方法或者语句进行访问的时候，其他的不能再对他进行操作了，必须等到这次访问结束以后才能对这个线程安全的方法进行访问。

**多个线程访问同一个对象时，调用这个对象的行为都可以获得正确的结果，那么这个对象就是线程安全的。**

线程安全问题都是由全局变量及静态变量引起的。

- 1.原子性：提供互斥访问，同一时刻只能有一个线程对数据进行操作，（atomic,synchronized）；
- 2.可见性：一个线程对主内存的修改可以及时地被其他线程看到，（synchronized,volatile）；
- 3.有序性：一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序，（happens-before原则）。

为保证线程安全，可以添加synchroized关键字，上Lock锁，加volatile关键字。



为什么会出现线程安全问题?

线程不安全:

* 当多线程并发访问临界资源时，如果破坏原子操作，可能会造成数据不一致。

* 临界资源:共享资源(同一对象)，一次仅允许一个线程使用，才可保证其正确性。
* 原子操作:不可分割的多步操作，被视作一个整体，其顺序和步骤不可打乱或缺省。

注：线程安全问题都是由全局变量及静态变量引起的。若每个线程中对全局变量、静态变量只有读操作，而无写操作, 一般来说，这个全局变量是线程安全的;若有多个线程同时执行写操作，一般都需要考虑线程同步，否则的话就可能影响线程安全。

```java
//线程不安全的实例
class Ticket implements Runnable {
    private int ticket = 100;

    Object object=new Object();
    @Override
    public void run() {
        while (true){//只要有票就一直卖
            if (ticket > 0) {
                //模拟一下出票时间
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "卖了第" + ticket--+"张");
            }
        }
    }
}

public  class ThreadSafe {
    public static  void main(String[] args) {
        //启动三个窗口
        Ticket ticket = new Ticket();
        Thread t1 = new Thread(ticket, "窗口1");
        Thread t2 = new Thread(ticket, "窗口2");
        Thread t3 = new Thread(ticket, "窗口3");
        t1.start();
        t2.start();
        t3.start();
    }
}


```

##### 6.2 解决方法一：同步代码块

```
语法: 
synchronized(临界资源对象){ /对临界资源对象加锁
	//代码(原子操作)
}
```

**同步锁:**
对象的同步锁只是一个概念可以想象为在对象上标记了一个锁.

1. 锁对象可以是任意类型。

2. 多个线程对象要使用同一把锁。

   注意在任何时候最多允许一个线程拥有同步锁 谁拿到锁就进入代码块，其他的线程只能在外等着(BLOCKED)。

```java
//用synchronized解决线程安全问题
class Ticket implements Runnable {
    private int ticket = 100;

    Object object=new Object();
    @Override
    public void run() {
        while (true){//只要有票就一直卖
            synchronized (object) {
                if (ticket > 0) {
                    //模拟一下出票时间
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + "卖了第" + ticket--+"张");
                }
            }
        }
    }
}

public  class ThreadSafe {
    public static  void main(String[] args) {
        //启动三个窗口
        Ticket ticket = new Ticket();
        Thread t1 = new Thread(ticket, "窗口1");
        Thread t2 = new Thread(ticket, "窗口2");
        Thread t3 = new Thread(ticket, "窗口3");
        t1.start();
        t2.start();
        t3.start();
    }
}


```

##### 6.3 解决方法二：同步方法

**同步方法:**使用synchronized修饰的方法就叫做同步方法，保证A线程执行该方法的时候其他线程只能在方法外等着。

```
语法:
synchronized 返回值类型方法名称 (形参列表){ //对当前对象(this)加锁
	//代码(原子操作)
}
```

* 只有拥有对象互斥锁标记的线程，才能进入该对象加锁的同步方法中。
* 线程退出同步方法时，会释放相应的互斥锁标记。如里方法是静态，锁是类名.class。

```java
class Ticket implements Runnable {
    private int ticket = 100;
    @Override
    public void run() {
        while (true){
            //调用同步方法
            sellTicket();
        }
    }

    public synchronized void sellTicket(){//synchronized修饰普通方法，有锁：this
        //相当于synchronized(this) {}
        if (ticket > 0) {
            //模拟一下出票时间
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "卖了第" + ticket--+"张");
        }
    }
}

public  class ThreadSafe {
    public static  void main(String[] args) {
        //启动三个窗口
        Ticket ticket = new Ticket();
        Thread t1 = new Thread(ticket, "窗口1");
        Thread t2 = new Thread(ticket, "窗口2");
        Thread t3 = new Thread(ticket, "窗口3");
        t1.start();
        t2.start();
        t3.start();
    }
}
```

```java

class Ticket implements Runnable {
    private static int ticket = 100;
    @Override
    public void run() {
        while (true){
            //调用同步方法
            sellTicket();
        }
    }

    /*public synchronized void sellTicket(){//synchronized修饰普通方法，有锁：this
        //相当于synchronized(this) {}
        if (ticket > 0) {
            //模拟一下出票时间
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "卖了第" + ticket--+"张");
        }
    }*/
    public synchronized static void sellTicket(){//synchronized修饰静态方法，有锁：Ticket.class
        //相当于synchronized(Ticket.class) {}
        if (ticket > 0) {
            //模拟一下出票时间
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "卖了第" + ticket--+"张");
        }
    }

}
```

##### 6.5 解决方法三：Lock

```java
class MyList {
    private Lock lock = new ReentrantLock();
    private String[] strArr = {"A", "B", "", "", ""};
    private int index = 2;

    public void add(String str) {
        lock.lock();//手动加锁
        strArr[index] = str;
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        index++;
        System.out.println(Thread.currentThread().getName() + "添加了" + str);
        lock.unlock();//释放锁
    }

    public String[] getStrArr() {
        return strArr;
    }
}

public class TestMyList {
    public static void main(String[] args) throws InterruptedException {
        MyList myList = new MyList();

        //匿名内部类
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                myList.add("hello");
            }
        };
        Runnable r2 = new Runnable() {
            @Override
            public void run() {
                myList.add("world");
            }
        };
        Thread t1 = new Thread(r1, "线程A");
        Thread t2 = new Thread(r2, "线程B");
        t1.start();
        t2.start();

        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        String[] strArr = myList.getStrArr();
        for (String str : strArr) {
            System.out.println(str);
        }
    }
}

```



1. **synchronized 的局限性与 Lock 的优点**

   如果一个代码块被synchronized关键字修饰，当一个线程获取了对应的锁，并执行该代码块时，其他线程便只能一直等待直至占有锁的线程释放锁。事实上，占有锁的线程释放锁一般会是以下三种情况之一：

   占有锁的线程执行完了该代码块，然后释放对锁的占有；
   占有锁线程执行发生异常，此时JVM会让线程自动释放锁；
   占有锁线程进入 WAITING 状态从而释放锁，例如在该线程中调用wait()方法等。

2. synchronized 是Java语言的内置特性，可以轻松实现对临界资源的同步互斥访问。那么，为什么还会出现Lock呢？试考虑以下三种情况：

   **Case 1 ：**在使用synchronized关键字的情形下，假如占有锁的线程由于要等待IO或者其他原因（比如调用sleep方法）被阻塞了，但是又没有释放锁，那么其他线程就只能一直等待，别无他法。这会极大影响程序执行效率。因此，就需要有一种机制可以不让等待的线程一直无期限地等待下去（比如只等待一定的时间 (解决方案：tryLock(long time, TimeUnit unit)) 或者能够响应中断 (解决方案：lockInterruptibly())），这种情况可以通过 Lock 解决。

   **Case 2 ：**我们知道，当多个线程读写文件时，读操作和写操作会发生冲突现象，写操作和写操作也会发生冲突现象，但是读操作和读操作不会发生冲突现象。但是如果采用synchronized关键字实现同步的话，就会导致一个问题，即当多个线程都只是进行读操作时，也只有一个线程在可以进行读操作，其他线程只能等待锁的释放而无法进行读操作。因此，需要一种机制来使得当多个线程都只是进行读操作时，线程之间不会发生冲突。同样地，Lock也可以解决这种情况 (解决方案：ReentrantReadWriteLock) 。

   **Case 3 ：**我们可以通过Lock得知线程有没有成功获取到锁 (解决方案：ReentrantLock) ，但这个是synchronized无法办到的。

   上面提到的三种情形，我们都可以通过Lock来解决，但 synchronized 关键字却无能为力。

3. 事实上，Lock 是 java.util.concurrent.locks包 下的接口，Lock 实现提供了比 synchronized 关键字 更灵活、更广泛、粒度更细的锁操作，它能以更优雅的方式处理线程同步问题。也就是说，Lock提供了比synchronized更多的功能。但是要注意以下几点：

   1) synchronized是Java的关键字，因此是Java的内置特性，是基于JVM层面实现的，其经过编译之后，会在同步块的前后分别形成 monitorenter 和 monitorexit 两个字节码指令；而Lock是一个Java接口，是基于JDK层面实现的，通过这个接口可以实现同步访问；

   2) 采用synchronized方式不需要用户去手动释放锁，当synchronized方法或者synchronized代码块执行完之后，系统会自动让线程释放对锁的占用；而 Lock则必须要用户去手动释放锁 (发生异常时，不会自动释放锁)，如果没有主动释放锁，就有可能导致死锁现象。

   这是很好理解的。Synchronized方式是Java原生支持的，开发人员在使用它来解决并发问题时，一定会方便很多，在这里，开发人员就不需要手动获取锁和释放锁，这些操作均有Java自身自动完成；而Lock方式是JDK层面的提供给开发人员的接口，因此开发人员在使用它来解决并发问题时，需要手动获取锁和释放锁。

#### 7.线程通信

<font size=4>**概述**</font>
多个线程在处理同一个资源，但是处理的动作(线程的任务)却不相同。
<font size=4>**为什么要处理线程间通信**</font>
多个线程并发执行时，在默认情况下CPU是随机切换线程的，当我们需要多个线程来共同完成一件任务,并且我们希望他们有规律的执行，那么多线程之间需要一些协调通信，
以此来帮我们达到多线程共同操作一份数据。
<font size=4>**如何保证线程间通信有效利用资源**</font>
多个线程在处理同一个资源，并且任务不同时，需要线程通信来帮助解决线程之间对同一个变量的使用或操作。就是多 个线程在操作同一份数据时，避免对同一共享变量的争
夺。也就是我们需要通过一定的手段使各个线程能有效的利用资源。而这种手段即---------**等待唤醒机制。**
<font size=4>**等待唤醒机制**</font>
什么是等待唤醒机制
这是多个线程间的一种协作机制。谈到线程我们经常想到的是线程间的**竞争(race)** ，比如去争夺锁，但这并不是故事的全部，线程间也会有协作机制。就好比在公司里你和你的同事们，你们可能存在在晋升时的竞争，但更多时候你们更多是一起合作以完成某些任务。
就是在一个线程进行了规定操作后，就进入等待状态(**wait()**），等待其他线程执行完他们的指定代码过后 再将其唤醒(**lnotify()**） ;在有多个线程进行等待时，如果需要，可以使用lnotifyAll()来唤醒所有的等待线程。wait/notify就是线程间的一种协作机制。

线程通信方法

| 方法                                  | 说明                                     |
| ------------------------------------- | ---------------------------------------- |
| public final void wait ()             | 释放锁，进入等待队列                     |
| public final void wait (long timeout) | 在超过指定的时间前，释放锁，进入等待队列 |
| public final void notify()            | 随机唤醒、通知一个线程                   |
| public final void notifyAll()         | 唤醒、通知所有线程                       |

注意:所有的等待、通知方法必须在对加锁的同步代码块中。
等待唤醒机制就是用于解决线程间通信的问题的，使用到的3个方法的含义如下:

1. wait:线程不再活动，不再参与调度，进入wait set(锁池)中，因此不会浪费CPU资源，也不会去竞争锁了，这时的线程状态即是WAITING,它还要等着别的线程执行一个特别的动作，也即是“通知(notify) ” 在这个对象上等待的线程从wait set中释放出来,重新进入到调度队列(ready queue)中
    wait(long m):wait方法如果在指定的毫秒之后，还没有被notify唤醒，就会自动醒来
    sleep(long m):不会释放锁
2. notify:则选取所通知对象的wait set中的一个线程释放;例如，餐馆有空位置后，等候就餐最久的顾客最先入座。
3. notifyAll:则释放所通知对象的wait set.上的全部线程。

```java
//奇偶线程：

public class PrintOddEven2 {

	private static int count = 0;
	private static final Object object = new Object();

	public static void main(String[] args) {
		new Thread(new printer(), "偶数线程，").start();
		new Thread(new printer(), "奇数线程，").start();
	}

	static class printer implements Runnable {

		@Override
		public void run() {
			while (count <= 100) {
				synchronized (object) {
					// 打印数字，并立即释放锁
					System.out.println(Thread.currentThread().getName() + "打印:" + count++);
					object.notify();
					// 此处判断，是为了打印完了100个数字后，程序能够正常结束，否则程序将一直等待下去，耗费系统资源。
					if (count <= 100) {
						try {
							object.wait();
						} catch (InterruptedException e) {
							e.printStackTrace();
						}
					}
				}
			}
		}
	}

}
```

注意
哪怕只通知了一个等待的线程,被通知线程也不能立即恢复执行，因为它当初中断的地方是在同步块内，而此刻它已经不持有锁，所以她需要再次尝试去获取锁(很可能面临其它线程的竞争)，成功后才能在当初调用wait方法之后的地方恢复执行。
总结如下:

* 如果能获取锁，线程就从WAITING状态变成RUNNABLE状态;
* 否则，从wait set出来，又进入entry set, 线程就从WAITING状态又变成BLOCKED状态
  调用wait和notify方法需要注意的细节

1. wait方法与notify方法必须要由同一个锁对象调用。因为:对应的锁对象可以通过notify唤醒使用同一个锁对象调用的wait方法后的线程。
2. wait方法与notify方法是属于Object类的方法的。因为:锁对象可以是任意对象，而任意对象的所属类都是继承了Object类的。
3. wait方法与notify方法必须要在同步代码块或者是同步函数中使用。因为:必须要通过锁对象调用这2个方法。

####  8.synchronized关键字

JDK提供锁分两种：一种是synchronized，依赖JVM实现锁，因此在这个关键字作用对象的作用范围内是同一时刻只能有一个线程进行操作；另一种是LOCK，是JDK提供的代码层面的锁，依赖CPU指令，代表性的是ReentrantLock。

**synchronized修饰的对象有四种：**

（1）修饰代码块，作用于调用的对象；

（2）修饰方法，作用于调用的对象；

（3）修饰静态方法，作用于所有对象；

（4）修饰类，作用于所有对象。

[synchronized用处](https://erxionglsx.github.io/#/./docs/Java进阶?id=synchronized用处)

- synchronized保证了线程的原子性。(被保护的代码块是一次被执行的，没有任何线程会同时访问)
- synchronized还保证了可见性。(当执行完synchronized之后，修改后的变量对其他的线程是可见的)
- synchronized还保证了有序性。

Java中的synchronized，通过使用内置锁，来实现对变量的同步操作，进而实现了**对变量操作的原子性和其他线程对变量的可见性**，从而确保了并发情况下的线程安全。

#### 9.死锁

同步锁：当多个线程同时访问同一个数据时，很容易出现问题。为了避免这种情况出现，我们要保证线程 同步互斥，就是指并发执行的多个线程，在同一时间内只允许一个线程访问共享数据。 Java 中可 以使用 synchronized 关键字来取得一个对象的同步锁。

死锁：何为死锁，就是多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。

![image-20220321233015498](C:\Users\HP\Desktop\笔记\Imag\死锁.png)

产生死锁的***四个必要条件***：
（1） 互斥条件：一个资源每次只能被一个进程使用。
（2） 占有且等待：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
（3）不可强行占有:进程已获得的资源，在末使用完之前，不能强行剥夺。
（4） 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。
这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之一不满足，就不会发生死锁。



#### 10. volatile关键字

[参考链接](https://www.cnblogs.com/dolphin0520/p/3920373.html)

volatile 关键字的主要作用就是保证变量的可见性和有序性然后还有一个作用是防止指令重排序。

volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。**这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值**。

有序性：有序性是指，在JVM中，允许编译器和处理器对指令进行重排序，但是重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。

可以通过volatile、synchronized、lock保证有序性。

**synchronized关键字和volatile关键字比较**：

1. volatile是线程同步的轻量级实现，所以性能比synchronized好。但是volatile只能修饰变量，synchronized可以修饰变量、代码块、方法（volatile保证变量可见性，synchronized保证代码块可见性）。

2. volatile用于解决变量多线程之间的可见性，synchronized是解决多线程之间访问资源的同步性。
3. 多线程访问volatile不会发生阻塞，synchronized会出现阻塞
4. **Volatile只能保证可见性，无法保证原子性**，Synchronized可以原子性和可见性

#### 11.线程池

**1.线程池的概念**

线程池就是首先创建一些线程，它们的集合称为线程池。使用线程池可以很好地提高性能，线程池在系统启动时即创建大量空闲的线程，程序将一个任务传给线程池，线程池就会启动一条线程来执行这个任务，执行结束以后，该线程并不会死亡，而是再次返回线程池中成为空闲状态，等待执行下一个任务。

**2.线程池的工作机制**

在线程池的编程模式下，任务是提交给整个线程池，而不是直接提交给某个线程，线程池在拿到任务后，就在内部寻找是否有空闲的线程，如果有，则将任务交给某个空闲的线程。

一个线程同时只能执行一个任务，但可以同时向一个线程池提交多个任务。

**3.使用线程池的原因**

多线程运行时间，系统不断的启动和关闭新线程，成本非常高，会过渡消耗系统资源，以及过渡切换线程的危险，从而可能导致系统资源的崩溃。这时，线程池就是最好的选择了。

**4.线程池的优势**

第一：**降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。

第二：**提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能执行。

第三：**提高线程的可管理性**。线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配、调优和监控。

**5.四种常见的线程池**

- CachedThreadPool:可缓存的线程池，该线程池中没有核心线程，非核心线程的数量为Integer.max_value，就是无限大，当有需要时创建线程来执行任务，没有需要时回收线程，适用于耗时少，任务量大的情况。
- SecudleThreadPool:周期性执行任务的线程池，按照某种特定的计划执行线程中的任务，有核心线程，但也有非核心线程，非核心线程的大小也为无限大。适用于执行周期性的任务。
- SingleThreadPool:只有一条线程来执行任务，适用于有顺序的任务的应用场景。
- FixedThreadPool:定长的线程池，有核心线程，核心线程的即为最大的线程数量，没有非核心线程。

概述
我们使用线程的时候就去创建一个线程， 这样实现起来非常简便，但是就会有一一个问题:
如果并发的线程数量很多，并且每个线程都是执行一个时间很短的任务就结束了，这样频繁创建线程就会大大降低系统的效率，因为频繁创建线程和销毁线程需要时间。
那么有没有一种办法使得线程可以复用，就是执行完一个任务, 并不被销毁,而是可以继续执行其他的任务?
在Java中可以通过线程池来达到这样的效果。
线程池:其实就是一个容纳多个线程的容器，其中的线程可以反复使用，省去了频繁创建线程对象的操作，无需反复创建线程而消耗过多资源。
合理利用线程池能够带来三个好处:
1.降低资源消耗。减少了创建和销毁线程的次数,每个工作线程都可以被重复利用，可执行多个任务。
2.提高响应速度。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
3.提高线程的可管理性。可以根据系统的承受能力，调整线程池中工作线线程的数目，防止因为消耗过多的内存，而把服务器累趴下(每个线程需要大约1MB内存，线程开的
越多，消耗的内存也就越大,最后死机)。

线程池的使用
Java里面线程池的顶级接口是java .util. concurrent. Executor,但是严格意义上讲Executor并不是一个线程池，而只是一个执行线程的工具。真正的线程池接口是java. util. concurrent . ExecutorService.
要配置一个线程池是比较复杂的，尤其是对于线程池的原理不是很清楚的情况下，很有可能配置的线程池不是较优的，因此在java .util. concurrent. Executors线程工厂类里面提供了一些静态工厂 ,生成一些常用的线程池。官方建议使用Executors工程类来创建线程池对象。Java类库提供了许多静态方法来创建一一个线程池: 
Executors类中创建线程池的方法如下:
a. newFixedThreadPool 创建一个固定长度的线程池， 当到达线程最大数量时,线程池的规模将不再变化。
b. newCachedThreadPool 创建一个可缓存的线程池，如果当前线程池的规模超出了处理需求，将回收空的线程;当需求增加时，会增加线程数量;线程池规模无限制。
C. newsingleThreadPoolExecutor创建一个单线程的Executor, 确保任务对了，串行执行
d. newScheduledThreadPool 创建一个固定长度的线程池， 而且以延迟或者定时的方式来执行，类似Timer;



使用线程池中线程对象的步骤:
1.创建线程池对象。
2.创建Runnable接口子类对象。(task)
3.提交Runnable接口子类对象。(take task)
获取到一个线程池ExecutorService对象,定义了一个使用线程池对象的方法如下:
public Future<?> submit (Runnable task) :获取线程池中的某一个线程对象， 并执行
Future接口:用来记录线程任务执行完毕后产生的结果。线程池创建与使用。
4.关闭线程池(一般不做。

#### N.this与Thread.currentThread()的区别





### hashmap

**具体见[参考文章](https://mp.weixin.qq.com/s/sv94zXCl7MU54VBQx8WQJw)**

#### 1. HashMap的底层数据结构

在JDK1.7中，由”数组+链表“组成，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。

在JDK1.8中，有“数组+链表+红黑树”组成。当链表过长，则会严重影响HashMap的性能，红黑树搜索时间复杂度是O(logn)，而链表是O(n)。因此，JDK1.8对数据结构做了进一步的优化，引入了红黑树，链表和红黑树在达到一定条件会进行转换：

- **当链表超过8且数组长度(数据总量)超过64才会转为红黑树**
- 将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树，以减少搜索时间。

<img src="C:\Users\HP\Desktop\笔记\Imag\HashMap.png" alt="hashmap原理" title="图片标题" style="zoom:70%;" />

#### 2. HashMap的特点

- hashmap存取是无序的
- 键和值位置都可以是null，但是键位置只能是一个null
- 键位置是唯一的，底层的数据结构是控制键的
- jdk1.8前数据结构是：链表+数组。jdk1.8之后是：数组+链表+红黑树
- **阈值（边界值）>8并且数组长度大于64**，才将链表转换成红黑树，变成红黑树的目的是提高搜索速度，高效查询

#### 3. 解决hash冲突的办法

解决Hash冲突方法有：开放定址法、再哈希法、拉链法（HashMap中常见的拉链法）、建立公共溢出区。HashMap中采用的是链地址法。

- 开放定址法也称为再散列法： 基本思想就是，如果p=H(key)出现冲突时，则以p为基础，再次hash，p1=H(p)，如果p1再次出现冲突，则以p1为基础，以此类推，直到找到一个不冲突的哈希地址pi。因此开放定址法所需要的hash表的长度要大于等于所需要存放的元素，而且因为存在再次hash，所以只能在删除的节点上做标记，而不能真正删除节点
- 再哈希法（双重散列，多重散列）：提供多个不同的hash函数，R1=H1(key1)发生冲突时，再计算R2=H2（key1），直到没有冲突为止。这样做虽然不易产生堆集，但增加了计算的时间。
- 拉链法： 将哈希值相同的元素构成一个同义词的单链表，并将单链表的头指针存放在哈希表的第i个单元中，查找、插入和删除主要在同义词链表中进行，链表法适用于经常进行插入和删除的情况。
- 建立公共溢出区：将哈希表分为公共表和溢出表，当溢出发生时，将所有溢出数据统一放到溢出区

注意开放定址法和再哈希法的区别是**开放定址法只能使用同一种hash函数进行再次hash，再哈希法可以调用多种不同的hash函数进行再次hash**

#### 4. 为什么要在数组长度大于64之后，链表才会进化为红黑树

在数组比较小时如果出现红黑树结构，反而会降低效率，而红黑树需要进行左旋右旋，变色，这些操作来保持平衡，同时数组长度小于64时，搜索时间相对要快些，总之是为了加快搜索速度，提高性能

JDK1.8以前HashMap的实现是数组+链表，即使哈希函数取得再好，也很难达到元素百分百均匀分布。当HashMap中有大量的元素都存放在同一个桶中时，这个桶下有一条长长的链表，此时HashMap就相当于单链表，假如单链表有n个元素，遍历的时间复杂度就从O（1）退化成O（n），完全失去了它的优势，为了解决此种情况，JDK1.8中引入了红黑树（查找的时间复杂度为O（logn））来优化这种问题。

#### 5. 为什么加载因子设置为0.75，初始化临界值是12？

散列表的装填因子一般记为 α，定义为一个表的装满程度，即α ＝表中记录数n / 散列表长度m。

**临界值（threshold） = 负载因子（loadFactor）* 容量（capacity）**

1、在数组定义好长度之后，负载因子越大，所能容纳的键值对个数也越大。loadFactory越趋近于1，那么数组中存放的数据（entry也就越来越多），数据也就越密集，也就会有更多的链表长度处于更长的数值，我们的查询效率就会越低，当我们添加数据，产生hash冲突的概率也会更高。loadFactory越小，越趋近于0，数组中个存放的数据(entry)也就越少，表现得更加稀疏 。0.75是对空间和时间效率的一种平衡选择

如果负载因子小一些比如是0.4，那么初始长度16*0.4=6，数组占满6个空间就进行扩容，很多空间可能元素很少甚至没有元素，会造成大量的空间被浪费

如果负载因子大一些比如是0.9，这样会导致扩容之前查找元素的效率非常低

loadfactory设置为0.75是经过多重计算检验得到的可靠值，可以最大程度的减少rehash的次数，避免过多的性能消耗

2、根据 HashMap 的扩容机制，它会保证 capacity 的值永远都是 2 的幂；

那么，**为了保证负载因子（loadFactor） \* 容量（capacity）的结果是一个整数，这个值是 0.75(3/4) 比较合理，因为这个数和任何 2 的幂乘积结果都是整数。**

####  6. 哈希表底层采用何种算法计算hash值？

hashCode方法是Object中的方法，所有的类都可以对其进行使用，首先底层通过调用hashCode方法生成初始hash值h1，然后将h1无符号右移16位得到h2，之后将h1与h2进行按位异或（^）运算得到最终hash值h3，之后将h3与(length-1)进行按位与（&）运算得到hash表索引

* Object类中的hashCode()该方法是一个本地方法，Java将调用本地方法库对此方法的实现

- 对于String、Integer等类复写了Object中的hashCode方法的类来说，有各自的实现方法

其他可以计算出hash值的算法有

- 平方取中法
- 取余数
- 伪随机数法

#### 7. 两个对象的hashCode相等

hashCode相等产生hash碰撞，hashCode相等会调用equals方法比较内容是否相等，内容如果相等则会进行覆盖，内容如果不等则会连接到链表后方，链表长度超过8且数组长度超过64，会转变成红黑树节点

#### 8. hashcode()和equal()

重写equal必须重写hashcode

https://zhuanlan.zhihu.com/p/30321358

Java对于eqauls方法和hashCode方法是这样规定的：   

> 1.如果两个对象相同，那么它们的hashCode值一定要相同；
>
>  2.如果两个对象的hashCode相同，它们并不一定相同（这里说的对象相同指的是用eqauls方法比较）。 
>     如不按要求去做了，会发现相同的对象可以出现在Set集合中，同时，增加新元素的效率会大大下降。
>  3.equals()相等的两个对象，hashcode()一定相等；equals()不相等的两个对象，却并不能证明他们的hashcode()不相等。
>
>  换句话说，equals()方法不相等的两个对象，hashcode()有可能相等（我的理解是由于哈希码在生成的时候产生冲突造成的）。反过来，hashcode()不等，一定能推出equals()也不等；hashcode()相等，equals()可能相等，也可能不等。

Hashset是继承Set接口，Set接口又实现Collection接口，这是层次关系。那么Hashset、Hashmap、Hashtable中的存储操作是根据什么原理来存取对象的呢？

下面以HashSet为例进行分析，我们都知道：在hashset中不允许出现重复对象，元素的位置也是不确定的。在hashset中又是怎样判定元素是否重复的呢？在java的集合中，判断两个对象是否相等的规则是：
​     1.判断两个对象的hashCode是否相等

​       如果不相等，认为两个对象也不相等，完毕
​       如果相等，转入2
​      （这一点只是为了提高存储效率而要求的，其实理论上没有也可以，但如果没有，实际使用时效率会大大降低，所以我们这里将其做为必需的。）

​     2.判断两个对象用equals运算是否相等
​      如果不相等，认为两个对象也不相等
​      如果相等，认为两个对象相等（equals()是判断两个对象是否相等的关键）
​      为什么是两条准则，难道用第一条不行吗？不行，因为前面已经说了，hashcode()相等时，equals()方法也可能不等，所以必须用第2条准则进行限制，才能保证加入的为非重复元素。

![为什么重写equals()时必须要重写hashcode()??(清晰明了!)_面试_02](C:\Users\HP\Desktop\笔记\Imag\hashcode&equal.png)

假如我们只重写了equals方法,那么在添加s3时,会进入if判断时,遍历entry数组,查找是否有相等的hashcode;
由于s1和s3是不同的对象,因此他们的hashcode不等,从而走下面的addEntry()把s3添加进set集合

这样就导致set失去了他不重复的特性!!

#### 9. HashMap的put方法流程

以jdk8为例，简要流程如下：

1. 首先根据key的值计算hash值，找到该元素在数组中存储的下标
2. 如果数组是空的，则调用resize进行初始化；
3. 如果没有哈希冲突直接放在对应的数组下标里
4. 如果冲突了，且key已经存在（equal方法），就覆盖掉value
5. 如果冲突后是链表结构，就判断该链表是否大于8，如果大于8并且数组容量小于64，就进行扩容；如果链表节点数量大于8并且数组的容量大于64，则将这个结构转换成红黑树；否则，链表插入键值对，若key存在，就覆盖掉value
6. 如果冲突后，发现该节点是红黑树，就将这个节点挂在树上

<img src="C:\Users\HP\Desktop\笔记\Imag\HashMap_put.png" style="zoom: 67%;" />

#### 10.HashMap的扩容方式

HashMap在容量超过负载因子所定义的容量之后，就会扩容。java里的数组是无法自己扩容的，**将HashMap的大小扩大为原来数组的两倍**



扩容之后原位置的节点只有两种调整

- 保持原位置不动（新bit位为0时）
- 散列原索引+扩容大小的位置去（新bit位为1时）

扩容之后元素的散列设置的非常巧妙，节省了计算hash值的时间，我们来看一 下具体的实现

![图片](https://mmbiz.qpic.cn/mmbiz_png/QCu849YTaIOtETfibvQv676xq7sxYRL96QIkzRp4YTIIIYHV10HicgfeaULMDR3ibkqVrPoVibPN5hBXvCVz7iaj4dQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

当数组长度从16到32，其实只是多了一个bit位的运算，我们只需要在意那个多出来的bit为是0还是1，是0的话索引不变，是1的话索引变为当前索引值+扩容的长度，比如5变成5+16=21

![图片](https://mmbiz.qpic.cn/mmbiz_png/QCu849YTaIOtETfibvQv676xq7sxYRL96CG8H85miaz6zdpQ0ia5VvQDTLWTKgWWJzIzmRtAbUgoT1ib9bBSAIov2w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

这样的扩容方式不仅节省了重新计算hash的时间，而且保证了当前桶中的元素总数一定小于等于原来桶中的元素数量，避免了更严重的hash冲突，均匀的把之前冲突的节点分散到新的桶中去



### Java集合

#### 1.Map

**HashMap**

最常用的Map,它根据键的HashCode 值存储数据,根据键可以直接获取它的值，具有很快的访问速度。HashMap最多只允许一条记录的键为Null(多条会覆盖);允许多条记录的值为 Null。非同步的。

**TreeMap**

能够把它保存的记录根据键(key)排序,默认是按升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。TreeMap不允许key的值为null。非同步的。 

```java
Iterator iter = map.entrySet().iterator();
while(iter.hasNext()) {
    Map.Entry entry = (Map.Entry)iter.next();
    // 获取key
    key = (String)entry.getKey();
        // 获取value
    integ = (Integer)entry.getValue();
}
```

```java
 
// 假设map是TreeMap对象
// map中的key是String类型，value是Integer类型
String key = null; 
Integer integ = null;
Iterator iter = map.keySet().iterator();
while (iter.hasNext()) {
        // 获取key
    key = (String)iter.next();
        // 根据key，获取value
    integ = (Integer)map.get(key);
}
```



**Hashtable**

与 HashMap类似,不同的是：**key和value的值均不允许为null;它支持线程的同步**，即任一时刻只有一个线程能写Hashtable,因此也导致了Hashtale在写入时会比较慢。


**LinkedHashMap**

保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的.在遍历的时候会比HashMap慢。key和value均允许为空，非同步的

#### 2.List

Java List 一共三个实现类： 分别是 ArrayList、Vector 和 LinkedList。

* ArrayList（数组） ArrayList 是最常用的 List 实现类，内部是通过数组实现的，它允许对元素进行快速随机访问。数组的缺点是每个元素之间不能有间隔，当数组大小不满足时需要增加存储能力，就要将已经有数 组的数据复制到新的存储空间中。当从 ArrayList 的中间位置插入或者删除元素时，需要对数组进 行复制、移动、代价比较高。因此，它适合随机查找和遍历，不适合插入和删除。 

  ArrayList扩容默认是1.5倍：**int newCapacity = oldCapacity + (oldCapacity >> 1); ** ArrayList扩容机制为：新容量为原容量的1.5倍取整，或者是新容量为旧容量加上旧容量右移一位，推荐后一种说法。

* Vector（数组实现、线程同步） **Vector 与 ArrayList 一样，也是通过数组实现的，不同的是它支持线程的同步，即某一时刻只有一 个线程能够写 Vecto**r，避免多线程同时写而引起的不一致性，但实现同步需要很高的花费，因此， 访问它比访问 ArrayList 慢。

*  LinkList（链表） LinkedList 是用链表结构存储数据的，很适合数据的动态插入和删除，随机访问和遍历速度比较 慢。另外，他还提供了 List 接口中没有定义的方法，专门用于操作表头和表尾元素，可以当作堆 栈、队列和双向队列使用。

#### 3.Set

set 注重独一无二的性质，该体系集合用于存储无序(存入和取出的顺序不一定相同)元素，值不能重复。对象的相等性本质是对象 hashCode 值（java 是依据对象的内存地址计算出的此序号）判断的，如果想要让两个不同的对象视为相等的，就必须覆盖 Object 的 hashCode方法和 equals 方 法。