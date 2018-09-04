# Java 基础
###  自动拆装箱

- 自动装箱
  - 自动将基本数据类型转换为包装器类型（例如 int 转为 Integer，使用 `Integer.valueOf(int)` 方法）；
- 自动拆箱
  - 自动将包装器类型转换为基本数据类型（例如 Integer 转为 int，使用 `Integer.intValue()` 方法）；
- 注意点
  - Integer、Short、Byte、Character、Long 这几个类 valueOf 方法实现类似【有缓存】；
  - Double、Float 的 valueOf 方法的实现是类似的【无缓存】；
  - "==" 运算符比较时注意
    - 当两个操作数都是包装器类型的引用，则是比较指向的是否是同一个对象；
    - 若其中有一个操作数是表达式（即包含算术运算）则比较的是数值（即会触发自动拆箱过程）；

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3780005.html

###  Exception & Error

示意图：

- Error
  - 无法处理的异常，比如 OutOfMemoryError，一般发生这种异常，JVM 会选择终止程序；
- Exception
  - unchecked exception（非检查异常），也称运行时异常（RuntimeException）
    - 比如常见的 NullPointerException、IndexOutOfBoundsException、IllegalArgumentException 等我们可以处理的异常；
    - 对于运行时异常，不要求必须进行异常捕获处理或者抛出声明，由程序员自行决定；
  - checked exception（检查异常）
    - 必须进行捕获处理，比如常见的 IOExeption 和 SQLException；
    - 对于非运行时异常如果不进行捕获或者抛出声明处理，编译都不会通过。

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3769804.html

###  接口 & 抽象类

- 接口
  - 接口中的变量隐式定义（且只能）为 `public static final`；
  - 方法隐式定义（且只能）为 `public abstract`；
- 抽象类
  - 抽象方法必须为 public 或者 protected（因为如果为 private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认为 public；
- 二者区别
  - 抽象类是对事物（类）的抽象，而接口是对行为的抽象；
  - 抽象类可包含静态代码块和静态方法，接口中不能；

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3811437.html

###  内部类

- 成员内部类

  - 可以无条件访问外部类的所有成员属性和成员方法（包括 private 成员和静态成员）【编译器会默认为成员内部类添加了一个指向外部类对象的引用】；
  - 外部类如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过对象访问；
  - 成员内部类是依附外部类而存在的，即，如果要创建成员内部类的对象，必须要存在一个外部类的对象；
- 局部内部类

  - 定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内（不能有 public、protected、private 及 static 等修饰符）；
- 匿名内部类

  - 匿名内部类是唯一一种没有构造器的类，使用范围有限，大部分用于接口回调；
- 静态内部类

  - 定义在一个类里面的类，只不过在类的前面多了一个 static 关键字；
  - 不依赖于外部类；
- 为什么局部内部类和匿名内部类只能访问局部 final 变量？
  - 生命周期，变量拷贝；
  - 如果局部变量的值在编译期间就可以确定，则直接在匿名内部里面创建一个拷贝。如果局部变量的值无法在编译期间确定，则通过构造器传参的方式来对拷贝进行初始化赋值；
  - 数据不一致问题；

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3811445.html

###  hashCode 方法

- 方法签名

```java
public native int hashCode();
```

- 特点
  - 如果两个对象的 hashcode 不等，则 equals 方法得到的结果必定为 false；
  - 如果两个对象的 hashcode 相等，则 equals 方法得到的结果未知；
  - 如果两个对象的 equals 方法得到的结果为 true，则两个对象的 hashcode 必定相等；
  - 如果两个对象的 equals 方法得到的结果为 false，则两个对象的 hashcode 不一定相等；

###  final 关键字

- 修饰类
  - final 修饰的类不能被继承【注意：final 类中的所有成员方法都会被隐式地指定为 final 方法】；
- 修饰方法
  - final 修饰的方法不能被重写【注意：类的 private 方法会隐式地被指定为 final 方法】；
- 修饰变量
  - 若是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；
  - 若是引用类型的变量，则在对其初始化之后不能再指向另一个对象。

###  static 关键字

- static 可以用来修饰类的成员方法、成员变量，另外可以编写 static 代码块来优化程序性能；
- static 变量也称作静态变量。静态变量与非静态变量的区别：静态变量被所有的对象共享，在内存中只有一个副本，当且仅当在类初次加载时被初始化；而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响；
- static 静态代码块以优化程序性能。static 块可以置于类中的任何地方，类中可以有多个 static 块。在类初次被加载的时候，会按照 static 块的顺序来执行每个 static 块，且只会执行一次；
- static 不能修饰局部变量（语法）；

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3799052.html

###  Java 引用类型

- 强引用（StrongReference）
  - 程序中普遍存在的，例如：
    - `Object object = new Object();`
    - `String str = "hello";`
  - 只要某个对象有强引用与之关联，JVM 必定不会回收这个对象，即使在内存不足的情况下，JVM 宁愿抛出 OutOfMemory 错误也不会回收这种对象；
- 软引用（SoftReference）
  - 描述一些有用但非必需的对象，用 `java.lang.ref.SoftReference` 类表示；
  - 对于软引用关联着的对象，只有在内存不足的时候 JVM 才会回收该对象。因此，这一点可以很好地用来解决 OOM 的问题，并且这个特性很适合用来实现缓存：比如网页缓存、图片缓存等；
- 弱引用（WeakReference）
  - 也用来描述非必需对象，用 `java.lang.ref.WeakReference` 类来表示；
  - 当 JVM 进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象；
- 虚引用（PhantomReference）
  - 虚引用和前面的软引用、弱引用不同，它并不影响对象的生命周期。用 `java.lang.ref.PhantomReference` 类表示；
  - 如果一个对象与虚引用关联，则跟没有引用与之关联一样，在任何时候都可能被垃圾回收器回收；
  - 虚引用必须和引用队列关联使用。

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3784171.html

