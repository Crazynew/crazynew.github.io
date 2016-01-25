---

layout: post
category: "Java"
title:  "疯狂java讲义学习——java基础类库"
tags: [疯狂java]

---

## 与用户互动

##### 运行java程序的参数

java程序的入口-main()方法`public static void main(String[] args)`：
* public——Java类由JVM调用，所以需要暴露该main方法；
* static——JVM直接通过该类来调用方法；
* void——因为主方法由JVM调用，该方法的返回值返回给JVM，没有任何意义；
* args——形参应该由JVM负责赋值。例如：调用时`java ClassName java String`，在类名后传入参数值；

##### 使用Scanner获取键盘输入

Scanner是一个基于正则表达式的文本扫描器，可以从文件、输入流、字符串中解析出基本类型值和字符串值。
主要方法有：

* hasNextXxx():是否还有下一个输入项，其中Xxx可以是Int、Long等代表基本数据类型的字符串。如果只是判断是否包含下一个字符串，则直接使用hasNext();
* nextXxx():获取下一个输入项。Xxx的含义与前一个方法中的Xxx相同；
* useDelimiter(String pattern):设置分隔符，默认是使用空白（包括空格、Tab空白、回车）作为输入项之间的分隔符；
* boolean hasNextLine():返回输入源中是否还有下一行；
* String nextLine():返回输入源中下一行的字符串；

## 系统相关

Java提供了System类和Runtime类来与程序的运行平台进行交互。

##### System类

System类代表当前java程序的运行平台，提供了一些类变量和类方法，允许直接通过System类来调用这些类变量和类方法。同时，System类提供了代标准输入、标准输入和错误输出的类变量，并提供了一些静态方法用于访问环境变量、系统属性的方法，还提供了加载文件和动态链接库的方法。

> 加载文件和动态链接库主要对native方法有用，对于一些特殊的功能（如访问操作系统底层硬件设备）java程序无法实现，必需借助C语言来完成，此时需要使用C语言为java方法提供实现，其实现步骤如下：

> * java程序中生命native修饰的方法，类似于abstract方法，只有方法签名，没有实现。编译该java程序，生成一个class文件。
> * 用javah编译第一步生成的class文件，将产生一个.h文件。
> * 写一个.cpp文件实现native方法，这一步需要包含上一步产生的.h文件（这个.h文件中又包含了jdk带的jni.h文件）
> * 将上一步的.cpp文件编译成动态链接库文件。
> * 在java中用System类的loadLibrary...()方法或者Runtime类的loadLibrary()方法加载上一步产生的动态链接库文件，java程序中就可以调用这个native方法了。

System类的常用方法：

* getenv():获取系统的所有环境变量;
* getProperties():获取所有系统属性；
* getProperty(String arg):获取特定的系统属性；
* gc():通知垃圾回收；
* runFinalization():通知系统进行资源清理

##### Runtime类

Runtime类代表java程序的运行时环境，可以访问JVM的相关信息，如处理器储量、内存信息等。每个java程序都有一个与之对应的Runtime实例，应用程序通过该对象与其运行时环境相连。应用程序不能创建自己的Runtime实例，但可以通过getRuntime()方法获取与之关联的Runtime对象。

## 常用类

##### Obejct类

Object类是所有类、数组、枚举类的父类。当定义一个类时默认集成Object父类。

常用方法如下：

* boolean equals(Object obj):判断指定对象与该对象是否相等。
* protected void finalize():垃圾回收器调用此方法来清理该对象的资源。
* Class<?> getClass():返回该对象的运行时类。
* int hashCode():返回该对象的hashCode值。
* String toString():返回该对象的字符串表示。【运行时类名@十六进制的hashCode值】
* wait()、notify()、notifyAll()几个方法主要用于控制线程的暂停和运行。
* clone():帮助其他对象来实现“自我克隆”，即得到一个当前对象的副本，而且二者之间完全隔离。【Object类提供的Clone机制只对对象里各实例变量进行“简单复制”。该方法简单高效易用，但只是一种“浅克隆”——只克隆该对象的所有成员变量值，不会对引用类型的成员变量值所引用的对象进行克隆】

#####　java 7 新增的Objects类

Objects工具类，提供一些工具方法来操作对象，这些方法大多是“空指针”安全的。

> 注：Java为工具类的命名习惯是添加一个字母s，比如操作数组的工具类是Arrays，操作集合的工具类是Collections

##### String、StringBuffer和StringBuilder类

1. String类是不可变类，即一旦创建之后，包含在这个对象中的字符序列是不可改变的，直至对象销毁。
2. StringBuffer对象则代表一个字符序列可变的字符串。其常用方法有append()、insert()、setCharAt()、setLength()等。
3. StringBuilder是JDK1.5新增的类，代表字符串对象，与StringBuffer相似，不同之处在于StringBuffer是线程安全的，而StringBuilder没有实现线程安全功能，所以性能略高。通常情况下，优先考虑使用StringBuilder类。

String类提供了大量构造器来创建String对象：

* String():创建一个包含0字符串序列的String对象（并非返回null）;
* String(byte[] bytes,Charset charset):使用指定的字符集将指定的byte[]数组解码成一个新的String对象；
* String(byte[] bytes,int offset,int length):使用平台默认字符集将指定的byte[]数组从offset开始，长度为length的子数组解码成一个新的String对象；
* String(byte[] bytes,int offset,int length,String charsetName):使用指定的字符集将指定的byte[]数组从offset开始，长度为length的子数组解码成一个新的String对象；
* String(byte[] bytes,String charsetName):使用指定的字符集将指定的byte[]数组解码成一个新的String对象；
* String(char[] value,int offset,int count):将指定的字符数组从offset开始、长度为count的字符元素连缀成字符串；
* String(String original):根据字符串直接量来创建一个String对象。也就是说，新创建的String对象是该参数字符串的副本；
* String(StringBuffer buffer):根据StringBuffer对象来创建对应的String对象；
* String(StringBuilderr builder):根据StringBuilder对象来创建对应的String对象；

String类提供大量方法来操作字符串对象：

* char charAt(int index):获取字符串中指定位置的字符；
* int compareTo(String anotherString):比较两个字符串的大小【字符序列相等，返回0；不相等时，从两个字符串第0个字符开始比较，返回第一个不相等的字符差；另外，较长字符串的前面部分恰巧是较短的字符串，则返回它们的长度差】；
* String concat(String str):将该String对象与str连接在一起。与Java提供的字符串运算符“+”的功能相同；
* boolean contentEquals(StringBuffer sb):将该String对象与StringBuffer对象sb进行比较，当它们包含的字符序列相同时返回true；
* static String copyValueOf(char[] data):将字符数组连缀成字符串，与String(char[] content)构造器功能相同；
* static String copyValueOf(char[] data,int offset,int count):将char数组的子数组中的元素连缀成字符串，与String(char[] value,int offset,int count)构造器的功能相同；
* boolean endsWith(String suffix):返回该String对象是否以suffix结尾；
* boolean equals(Object anObject):将该字符串与指定对象比较，如果二者包含的字符序列相等，则返回true，否则返回false;
* boolean eequalsIngnoreCase(String str):与前一个方法基本类似，只是忽略字符的大小写；
* byte[] getBytes():将该String对象转换成byte数组；
* void getChars(int srcBegin,int srcEnd,char[] dst,int dstBegin):该方法将字符串中从srcBegin开始到srcEnd结束的字符复制到dst字符数组中，其中dstBegin为目标字符数组的起始复制位置；
* int indexOf(int ch):找出ch字符在该字符串中第一次出现的位置；
* int indexOf(int ch,int fromIndex):找出ch字符在该字符串中从fromIndex开始后第一次出现的位置；
* int indexOf(String str):找出str子字符串在该字符串中第一次出现的位置；
* int indexOf(String str,int fromIndex):找出str子字符串在该字符串中从fromIndex开始后第一次出现的位置；
* int lastIndexOf(int ch):找出ch字符在该字符串中最后一次出现的位置；
* int lastIndexOf(int ch,int fromIndex):找出ch字符在该字符串中从fromIndex开始后最后一次出现的位置；
* int lastIndexOf(String str):找出str子字符串在该字符串中最后一次出现的位置；
* int lastIndexOf(String str,int fromIndex):找出str子字符串在该字符串中从fromindex开始后最后一次出现的位置；
* int length();返回当前字符串长度；
* String replace(char oldChar,char newChar):将字符串中的第一个oldChar替换成newChar;
* boolean startsWith(String prefix):该String对象是否以prefix开始；
* boolean startsWith(String prefix,int toffset):该String对象从toffset位置算起，是否以prefix开始；
* String substring(int beginIndex):获取从beginIndex位置开始到结束的子字符串；
* String substring(int beginIndex,int endIndex):获取从beginIndex到endIndex位置的子字符串；
* char[] toCharArray():将该String对象转换成char数组；
* String toLowerCase():将字符串转换成小写；
* String toUpperCase():将字符串转换成大写；
* static String valueOf(X x):一系列用于将基本类型转换为String对象的方法；

> 注:String是不可变的，所以会额外产生很多临时变量，使用StringBuffer和StringBuilder就可以避免该问题；

##### Math类

Math是一个工具类，完成复杂的运算。它的构造器被定义成private的，无法创建Math类的对象；Math类中的所有方法都是类方法，可以直接通过类名来调用它们。具体方法查阅API。

##### Java 7 的ThreadLocalRandom与Random

Random类专门用于生成一个伪随机数，它有两个构造器：一个构造器使用默认的种子（以当前时间作为种子），另一个构造器需要程序猿显式传入一个long型整数的种子；

ThreadLocalRandom类是Java 7新增的一个类，它是Random的增强版。在并发访问的环境下，使用ThreadLocalRandom来代替Random可以减少多线程资源竞争，最终保证系统具有更好的线程安全性；

ThreadLocalRandom类的用法与Random类的用法基本类似，它提供了一个静态的current()方法来获取ThreadLocaolRandom对象，获取该对象之后即可调用各种nextXxx()方法获取伪随机数了；

> 注：只要两个Random对象的种子相同，而且方法的调用顺序也相同，它们就会产生相同的数字序列。即Random产生的数字并不是真正随机的，而是一种伪随机；

#####　BigDecimal类

为了精确表示、计算浮点数，Java提供了BigDecimal类，该类提供了大量的构造器用于创建BigDecimal对象，包括把所有的基本数值型变量转换成一个i额BigDecimal对象，也包括利用数字字符串、数字字符数组来创建BigDecimal对象；通常建议优先使用基于String的构造器；

> 注：创建BigDecimal对象时，不要直接使用double浮点数作为构造器参数来调用BigDecimal构造器，否则同样会发生精度丢失的问题；

## Java 8的日期、时间类

##### Date类

Date处理日期、时间（此处的Date指java.util包下的Date类），提供了6个构造器，其中4个已经Deprecated（Java不再推荐使用，使用不再推荐的构造器时编译会提出警告系想你，并导致程序性能、安全性等方面的问题），剩下的两个构造器如下：

* Date():生成一个代表当前日期时间的Date对象。该构造器在底层调用System.currentTimeMillis()获得long整数作为日期参数；
* Date(long date):根据指定的long型整数来生成一个Date对象。该构造器的参数表示创建的Date对象和GMT1970年1月1日00:00:00之间的时间差，以毫秒作为计时单位；

同时，Date的大部分方法也Deprecated，剩下为数不多的几个方法如下：

* boolean after(Date when)：测试该日期是否在指定日期when之后；
* boolean before(Date when):测试该日期是否在指定日期when之前；
* long getTime():返回该时间对应的long型整数，即从GMT1970-01-01 00：00：00到该Date对象之间的时间差，以毫秒作为计时单位；
* void setTime(long time):设置该Date对象的时间；

总体来说，Date是一个设计相当糟糕的类，因此Java官方推荐尽量少用Date的构造器和方法。如果需要对日期、时间进行加减运算，或获取指定时间的年、月、日、时、分、秒信息，可使用**Calendar类**

##### Calendar类

Calendar是一个抽象类，用于表示日历，它是所有日历类的模板，并提供了一些所有日历通用的方法，但它本身不能直接实例化，程序只能创建Calendar子类的实例。作为一个抽象类，不能使用构造器来创建Calendar对戏那个，但它提供了几个静态getInstance()方法来获取Calendar对象。

Calendar类提供了大量访问、修改日期时间的方法，常用方法如下：

* void add(int field,int amount):根据日历的规则，为给定的日历字段添加或减去指定的时间量；
* int get(int field):返回指定日历字段的值；
* int getActualMaximum(int field):返回指定日历字段可能拥有的最大值；
* int getActualMinimum(int field):返回指定日历字段可能拥有的最小值；
* void roll(int field,int amount):与add()方法类似，区别在于加上amount后超过了该字段所能表示的最大范围时，也不会向上一个字段进位；
* void set(int field,int value):将给定的日历字段设置为给定值；
* void set(int year,int month,int date):设置Calendar对象的年月日三个字段的值；
* void set(int year,int month,int date,int hourOfDay,int minute,int second):设置Calendar对象的年、月、日、时、分、秒6个字段的值；

> 注：Calendar.MONTH字段代表月份，月份的起始值不是1，而是0，所以要设置8月时，用7而不是8.

Calendar类的几个注意点：

(1) add与roll的区别：
   
add(int field,int amount)的两条规则

   * 当被修改的字段超出它允许的范围时，会发生进位，即上一级字段也会增大
   * 如果下一级字段也需要改变，那么该字段会修正到变化最小的值
   
roll规则：
   
   * 当修改的字段超出它允许的范围时，上一级字段不会增大
   * 下一级字段的处理规则与add()相似
   
(2) 设置Calendar的容错性

Calendar提供了一个setLenient()用于设置它的容错性，Calendar默认支持较好的容错性，通过setLenient(false)可以关闭Calendar的容错性，让它进行严格的参数检查；

(3) set()方法的延迟修改

set(f,value)，尽管日历字段f是立即更改的，但该Calendar所代表的时间却不会立即修改，直到下次调用get()、getTime()、getTimeMillis()、add()或roll()时才会重新计算日历时间。这被称为set()方法的延迟修改，采用延迟修改的优势是多次调用set()不会触发多次不必要的计算。

##### Java 8新增的日期、时间包

Java 8专门新增了一个java.time包，该包下包含如下常用的类：

* Clock:该类用于获取指定时区的当前日期、时间；
* Duration:该类代表持续时间，该类可以非常方便地获取一段时间；
* Instant:代表一个具体的时刻，可以精确到纳秒；
* LocalDate:该类代表不带时区的日期，如2007-12-03；
* LocalTime:该类代表不带时区的时间，如10：15：30；
* LocalDateTime:该类代表不带时区的日期、时间，例如2007-12-03T10:15:30;
* MonthDay:该类仅代表月日，如--04-12；
* Year:该类代表年，如2014；
* YearMonth:该类代表年月，如2014-04；
* ZonedDateTime:该类代表一个时区化的日期、时间；
* ZoneId:该类代表一个时区；
* DayOfWeek:这是一个枚举类，定义了周日到周六的枚举值；
* Month:这也是一个枚举类，定义了一月到十二月的枚举值；

## 正则表达式

正则表达式是一个强大的字符串处理工具，可以对字符串进行查找、提取、分割、替换等操作；Java提供了Pattern和Matcher两个类专门用于提供正则表达式支持；正则表达式是一个用于匹配字符串的模板；

##### 创建正则表达式

所谓创建正则表达式就是创建一个特殊的字符串；

正则表达式所支持的合法字符：

<table>
  <tr>
    <th>字符</th>
    <th>解释</th>
  </tr>
  <tr>
    <td>x</td>
    <td>字符x(x可代表任何合法的字符)</td>
  </tr>
  <tr>
    <td>\0mnn</td>
    <td>八进制数0mnn所表示的字符</td>
  </tr>
  <tr>
    <td>\xhh</td>
    <td>十六进制0xhh所表示的字符</td>
  </tr>
  <tr>
    <td>\uhhhh</td>
    <td>十六进制0xhhhh所表示Unicode字符</td>
  </tr>
  <tr>
    <td>\t</td>
    <td>制表符('\u0009')</td>
  </tr>
  <tr>
    <td>\n</td>
    <td>新行(换行)符('\u000A')</td>
  </tr>
  <tr>
    <td>\r</td>
    <td>回车符('\u000D')</td>
  </tr>
  <tr>
    <td>\f</td>
    <td>换页符('\u000C')</td>
  </tr>
  <tr>
    <td>\a</td>
    <td>报警（bell）符('\u0007')</td>
  </tr>
  <tr>
    <td>\e</td>
    <td>Escape符('\u001B')</td>
  </tr>
  <tr>
    <td>\cx</td>
    <td>x对应的控制符。例如，\cM匹配Ctrl-M。x值必需为A-Z或者a-z之一</td>
  </tr>
</table>

正则表达式所支持的特殊字符：

<table>
  <tr>
    <th>特殊字符</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>$</td>
    <td>匹配一行的结尾。要匹配$字符本身，请使用\$</td>
  </tr>
  <tr>
    <td>^</td>
    <td>匹配一行的开头。要匹配^字符本身，请使用\^</td>
  </tr>
  <tr>
    <td>()</td>
    <td>标记子表达式的开始和结束位置。要匹配这些字符，请使用\(和\)</td>
  </tr>
  <tr>
    <td>[]</td>
    <td>用于确定中括号表达式的开始和结束位置。要匹配这些字符，请使用\[和\]</td>
  </tr>
  <tr>
    <td>{}</td>
    <td>用于标记前面子表达式的出现频度。要匹配这些字符，请使用\{和\}</td>
  </tr>
  <tr>
    <td>*</td>
    <td>指定前面子表达式可以出现零次或多次。要匹配*字符本身，请使用\*</td>
  </tr>
  <tr>
    <td>+</td>
    <td>指定前面子表达式可以出现一次或多次。要匹配+字符本身，请使用\+</td>
  </tr>
  <tr>
    <td>？</td>
    <td>指定前面子表达式可以出现零次或一次。要匹配？字符本身，请使用\？</td>
  </tr>
  <tr>
    <td>.</td>
    <td>匹配除换行符\n之外的任何单字符。要匹配.字符本身，请使用\.</td>
  </tr>
  <tr>
    <td>\</td>
    <td>用于转义下一个字符，或指定八进制、十六进制。如果匹配\字符，请用\\</td>
  </tr>
  <tr>
    <td>|</td>
    <td>指定两项之间任选一项。如果要匹配|字符本身，请使用\|</td>
  </tr>
</table>

> 注："\u0041\\\\"表示匹配A\，之所以有这么多反斜杠，是由于java字符串中反斜杠本身需要转义，因此两个反斜杠(\\)实际上相当于一个（前一个用于转义）。

预定义字符：

<table>
  <tr>
    <th>预定义字符</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>.</td>
    <td>可以匹配任何字符</td>
  </tr>
  <tr>
    <td>\d</td>
    <td>匹配0-9所有数字</td>
  </tr>
  <tr>
    <td>\D</td>
    <td>匹配非数字</td>
  </tr>
  <tr>
    <td>\s</td>
    <td>匹配所有的空白字符、包括空格、制表符、回车符、换页符、换行符等</td>
  </tr>
  <tr>
    <td>\S</td>
    <td>匹配所有的非空白字符/td>
  </tr>
  <tr>
    <td>\w</td>
    <td>匹配所有的单词字符，包括0-9所有的数字、26个英文字母和下划线(_)</td>
  </tr>
  <tr>
    <td>\W</td>
    <td>匹配所有的非单词字符</td>
  </tr>
</table>

方括号表达式：

<table>
  <tr>
    <th>方括号表达式</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>表示枚举</td>
    <td>例如[abc]，表示a、b、c其中任意一个字符</td>
  </tr>
  <tr>
    <td>表示范围：-</td>
    <td>例如[a-f]表示a-f范围内的任意字符；如[a-cx-z]表示a-c、x-z范围内的任意字符</td>
  </tr>
  <tr>
    <td>表示求否：^</td>
    <td>例如[^abc],表示非a、b、c的任意字符；[^a-f]，表示不是a-f范围内的任意字符</td>
  </tr>
  <tr>
    <td>表示“与”运算:&&</td>
    <td>例如[a-z&&[def]]求a-z和[def]的交集，表示d、e、f</td>
  </tr>
  <tr>
    <td>表示“并”运算</td>
    <td>并运算与前面的枚举类似，例如[a-d[m-p]],表示[a-dm-p]</td>
  </tr>
</table>

圆括号表达式用于将多个表达式组成一个子表达式，圆括号中可以使用或运算符(|)。例如，正则表达式"((public)|(protected)|(private))"用于匹配java的三个访问控制符其中之一；

边界匹配符：


<table>
  <tr>
    <th>边界匹配符</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>^</td>
    <td>行的开头</td>
  </tr>
  <tr>
    <td>$</td>
    <td>行的结尾</td>
  </tr>
  <tr>
    <td>\b</td>
    <td>单词的边界</td>
  </tr>
  <tr>
    <td>\B</td>
    <td>非单词的边界</td>
  </tr>
  <tr>
    <td>\A</td>
    <td>输入的开头</td>
  </tr>
  <tr>
    <td>\G</td>
    <td>前一个匹配的结尾</td>
  </tr>
  <tr>
    <td>\Z</td>
    <td>输入的结尾，仅用于最后的结束符</td>
  </tr>
  <tr>
    <td>\z</td>
    <td>输入的结尾</td>
  </tr>
</table>

正则表达式支持的数量表示符有如下几种模式：

* Greedy(贪婪模式):数量表示符默认采用贪婪模式，除非另有表示。贪婪模式的表达式会一直匹配下去，直到无法匹配。
* Reluctant(勉强模式):用问号后缀(?)表示，它只会匹配最少的字符。也称为最小匹配模式。
* Possessive(占有模式):用加号后缀(+)表示，目前只有Java支持占有模式，通常比较少用。

三种模式的数量表示符：

<table>
  <tr>
    <th>贪婪模式</th>
    <th>勉强模式</th>
    <th>占用模式</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>X?</td>
    <td>X??</td>
    <td>X?+</td>
    <td>X表达式出现零次或一次</td>
  </tr>
  <tr>
    <td>X*</td>
    <td>X*?</td>
    <td>X*+</td>
    <td>X表达式出现零次或多次</td>
  </tr>
  <tr>
    <td>X+</td>
    <td>X+?</td>
    <td>X++</td>
    <td>X表达式出现一次多多次</td>
  </tr>
  <tr>
    <td>X{n}</td>
    <td>X{n}?</td>
    <td>X{n}+</td>
    <td>X表达式出现n次</td>
  </tr>
  <tr>
    <td>X{n,}</td>
    <td>X{n,}?</td>
    <td>X{n,}+</td>
    <td>X表达式最少出现n次</td>
  </tr>
  <tr>
    <td>X{n,m}</td>
    <td>X{n,m}?</td>
    <td>X{n,m}+</td>
    <td>X表达式最少出现n次，最多出现m次</td>
  </tr>
</table>

##### 使用正则表达式

定义了正则表达式之后，就可以使用Pattern和Matcher来使用正则表达式。
<code>
  <pre>
  //将一个字符串编译成Pattern对象
  Pattern p = Pattern.compile("a*b");
  //使用Pattern对象创建Matcher对象
  Matcher m = p.matcher("aaaaaab");
  boolean b = m.matches();//返回true
  </pre>
</code>
上面定义的Pattern对象可以多次重复使用，如果某个正则表达式仅需一次使用，则可以直接使用Pattern类的静态matches()方法，但这种语句每次都需要重新百衲衣新的Pattern对象，不能重复利用已编译的Pattern对象，所以效率不高。如下所示:
<code>
 <pre>
  boolean b = Pattern.matches("a*b","aaaaaab"); //返回true
 </pre>
</code>

Pattern是不可变类，可供多个并发线程安全使用。

Matcher类提供了如下几个常用方法：

* find():返回目标字符串中是否包含与Pattern匹配的子串；
* group():返回上一次与Pattern匹配的子串；
* start():返回上一次与Pattern匹配的子串在目标字符串中的开始位置；
* end():返回上一次与Pattern匹配的子串在目标字符串中的结束位置加1；
* lookingAt():返回目标字符串前面部分与Pattern是否匹配；
* matches():返回整个目标字符串是否与Pattern是否匹配；
* reset():将现有的Matcher对象应用于一个新的字符序列;

> 注：通过Matcher类的find()和group()方法可以从目标字符串中依次取出特定子串。

## 国际化与格式化

引入国际化的目的是为了提供自适应、更友好的用户界面，并不需要改变程序的逻辑功能。

##### Java国际化的思路

Java程序的国际化思路是将程序中的标签、提示等系想你放在资源文件中。资源文件是key-value对，每个资源文件中的key是不变的，但value则随不同的国家、语言而改变。

Java程序的国际化主要通过以下三个类完成：

* java.util.ResourceBundle:用于加载国家、语言资源包；
* java.util.Locale:用于封装特定的国家/区域、语言环境；
* java.text.MessageFormat:用于格式化带占位符的字符串；

#####　Java支持的国家和语言

获取Java所支持的国家和语言，则可调用Locale类的getAvailableLocales()方法，该方法返回一个Locale数组，包含了java所支持的国家和语言。

##### 完成程序国际化

希望程序完成国际化，只需要将不同的国家/语言(Locale)的提示信息分别以不同的文件存放即可。java程序国际化的关键类是ResourceBundle和Locale。
<code>
 <pre>
 public class Hello
 {
    public static void main(String[] args)
    {
       Locale myLocale = Locale.getDefault(Locale.Category.FORMAT);
       ResourceBundle bundle = ResourceBundle.getBundle("mess",myLocale);
       System.out.println(bundle.getString("hello"));
    }
 }
 </pre>
<code>

##### 使用MessageFormat处理包含占位符的字符串

对于占位符的消息字符串，只需要使用MessageFormat类的format()方法为消息中的占位符指定参数即可。

##### 使用类文件代替资源文件

使用类文件来代替资源文件必需满足如下条件：

* 该类的类名必需是baseName_language_country，这与属性文件的命名相似；
* 该类必需集成listResourceBundle,并重写getContents()方法，该方法返回Object数组，该数组的每一项都是key-value对。

注：如果系统同时存在资源文件、类文件，系统将以类文件为主，而不会调用资源文件。

##### 使用NumberFormat格式化数字

NumberFormat和DateFormat可以将数值、日期转换成字符串，也可以将字符串转换成数值、日期。其都包含format()和parse()方法。format()用于将数值、日期格式化成字符串，parse()用于将字符串解析成数值、日期。

NumberFormat是一个抽象基类，所以无法通过它的构造器来创建NumberFormat对象，他/她提供了几个类方法来得到NumberFormat对象：

* getCurrencyInstance():返回默认Locale的货币格式器
* getIntegerInstance():返回默认Locale的整数格式器；
* getNumberInstance():返回默认Locale的通用数值格式器；
* getPercentInstance():返回默认Locale的百分数格式器；


##### 使用DateFormat格式化日期、时间

DateFormat也是一个抽象类，提供了如下几个类方法用于获取DateFormat对象：

* getDateInstance():返回一个日期格式器，他/她格式化的字符串只有日期，没有时间。
* getTimeInstance():返回一个时间格式器，他/她格式化后的字符串只有时间，没有日期；
* getDateTimeInstance():返回一个日期、时间格式器，它格式化的字符串既有日期，也有时间。

##### 使用SimpleDateFormat格式化日期

SimpleDateFormat是DateFormat的子类，是“简单”的日期格式器，功能更加强大。

## Java 8新增的日期、时间格式器

在java.time.format包下提供的DateTimeFormatter格式器类，相当于前面介绍的DateFormat和SimpleDateFormat的合体，功能非常强大。

## 更多详细基础类库可查阅API文档



    





