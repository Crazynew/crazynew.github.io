---

layout: post
category: "Java"
title:  "疯狂java讲义学习——java集合"
tags: [疯狂java]

---

## Java集合概述

为了保存数量不等的数据，以及保存具有映射关系的数据（即关联数组），Java提供了集合类（容器类），均位于java.util包下，Java 5开始在java.util.concurrent包下还提供了一些支持多线程的集合类。集合里只能保存对象（实际上只是保存对象的引用变量）。Java的集合类主要由两个接口派生：Collection和Map。

Collection集合体系的继承树：

![](http://i8.tietuku.com/0920a6c6ac1bb838.png)

Map体系的继承树：

![](http://i8.tietuku.com/610dc7fa98a11e89.png)

Set代表无序、不可重复的集合；List代表有序、重复的集合；Map则代表具有映射关系的集合；Java 5又增加了Queue体系集合，代表一种队列集合实现；

三种主要的集合示意图：

![](http://i4.tietuku.com/7258674748eaf3ed.png)

## Collection和Iterator接口

Collection作为接口List、Set、Queue的父接口，其定义了如下操作集合元素的方法：

* boolean add(Object o):用于向集合里添加一个元素，如果添加成功，则返回true；
* boolean addAll(Collection c):把集合c里的所有元素添加到指定集合里，如果集合对象被添加操作改变了，则返回true；
* void clear():清楚集合里的所有元素，集合长度变为0；
* boolean contains(Object o):返回集合里是否包含指定元素；
* boolean containsAll(Collection c):返回集合里是否包含集合c的所有元素；
* boolean isEmpty():返回集合是否为空，当长度为0时，返回true，否则false；
* Iterator iterator():返回一个Iterator对象，用于遍历集合里的元素；
* boolean remove(Object o):删除集合中的指定元素o，当集合中包含了一个或多个元素0
* 时，该方法只删除第一个符合条件的元素，该方法将返回true；
* boolean removeAll(Collection c):从集合里删除集合c里包含的所有元素，“求差集”；
* boolean retainAll(Collection c):从集合中删除集合c里不包含的元素，“求交集”；
* int size():返回集合里的元素个数；
* Object[] toArray():将集合转换为一个数组，所有的集合元素变成对应的数组元素；

##### 使用Lambda表达式遍历集合

Java 8为Iterable接口新增了一个forEach(Consumer action)默认方法，该方法所需参数类型是一个函数式接口，而Iterable是Collection接口的父接口，所以Collection集合也可以直接调用该方法。
<code>
 <pre>
 public class CollectionEach
 {
   public static void main(String[] args)
   {
     Collection books = new HashSet();
     books.add("轻量级java");
     books.add("疯狂java讲义");
     books.forEach(obj -> System.out.println("迭代集合元素：" + obj));
   }
 }
 </pre>
</code>

##### 使用Java 8增强的Iterator遍历集合元素

Iterator接口主要用于遍历（即迭代访问）Collection集合中的元素，Iterator对象也被成为迭代器，此接口中定义了如下4个方法：

* boolean hasNext():如果被迭代的集合元素还没有被遍历完，则返回true；
* Object next():返回集合里的下一个元素；
* void remove():删除集合里上一次next方法返回的元素；
* void forEachRemaining(Consumer action):这是Java 8为Iterator新增的默认方法，可使用Lambda表达式来遍历集合元素；

Iterator必需依附于Collection对象，且Iterator迭代器采用的是快速失败（fail-fast）机制，一旦在迭代过程中检测到该集合已经被修改（通常是程序中的其他线程修改），程序立即引发ConcurrentModificationException异常。

##### 使用Lambda表达式遍历Iterator
程序调用Iterator的forEachRemaining(Consumer action)遍历集合元素
<code>
 <pre>
 public class IteratorEach
 {
   public static void main(String[] args)
   {
     Collection books = new HashSet();
     books.add("轻量级java");
     books.add("疯狂java讲义");
     
     Iterator it = books.iterator();
     it.forEachRemaining(obj -> System.out.println("迭代集合元素：" + obj));
   }
 }
 </pre>
</code>

##### 使用foreach循环遍历集合元素

也可以使用java 5提供的foreach循环迭代访问集合元素：`for(Object obj : CollectionVar){}`

##### 使用Java 8新增的Predicate操作集合
Java 8为Collection集合新增了一个removeIf(Predicate filter)方法，该方法将会批量删除符合filter条件的所有元素,例如`books.removeIf(ele -> ((String)ele).length() < 10)`,将会删除所有长度小于10的字符串元素。更多Predicate用法举例(计算符合特定条件的统计个数)如下：
<code>
 <pre>
 public class PredicateTest
 {
   public static void main(String[] args)
   {
     Collection books = new HashSet();
     books.add("轻量级java");
     books.add("疯狂java讲义");
     System.out.println(calAll(books,ele -> ((String)ele).contains("疯狂")));
   }
   public static int calAll(Collection books,Predicate p)
   {
     int total = 0;
     for(Object obj : books)
     {
        if(p.test(obj))
           ++total;
     }
     return total;
   }
 }
 </pre>
</code>

##### 使用Java 8新增的Stream操作集合
Java8新增的Stream、IntStream、LongStream、DoubleStream等流式API，这些API代表多个支持穿行和并行聚集操作的元素。Java8为每个流式API提供了对应的Builder。

独立使用Builder的步骤如下：

* 使用Stream或XxxStream的builder()类方法创建该Stream对应的Builder
* 重复调用Builder的add()方法向该流中添加多个元素；
* 调用Builder的build()方法获取对应的Stream；
* 调用Stream的聚集方法；


Stream提供了大量的方法进行聚集操作，包括两种类型：

* 中间方法：中间操作允许流保持打开状态，并允许直接调用后续方法；
* 末端方法：末端方法是对流的最终操作，当对某个Stream执行末端方法后，该流将会被“消耗”且不可再用。

Stream常用的中间方法：

* filter(Predicate predicate):过滤Stream中所有不符合predicate的元素；
* mapToXxx(ToXxxFunction mapper):使用ToXxxFunction对流中的元素执行一对一的转换，该方法返回的新流中包含了ToXxxFunction转换生成的所有元素；
* peek(Consumer action):一次对每个元素执行一些操作，该方法返回的流与原有流包含相同的元素，该方法主要用于调试；
* distinct():该方法用于排序流中所有重复的元素；
* sorted():该方法用于保证流中的元素在后续的访问中处于有序状态；
* limit(long maxSize):该方法用于保证对流的后续访问中最大允许访问的元素个数；

Stream常用的末端方法：

* forEach(Consumer action):遍历流中的所有元素，对每个元素执行action；
* toArray():将流中所有元素转换为一个数组；
* reduce():该方法有三个重载的版本，都用于通过某种操作来合并流中的元素；
* min():返回流中所有元素的最小值;
* max():返回流中所有元素的最大值;
* count():返回流中所有元素的数量；
* anyMatch(Predicate predicate):判断流中是否至少包含一个元素符合predicate条件；    
* allMatch(Predicate predicate):判断流中是否每个元素符合predicate条件；
* noneMatch(Predicate predicate):判断流中是否所有元素都不符合predicate条件；
* findFirst():返回流中的第一个元素；
* findAny():返回六种的任意一个元素；

Java 8允许使用流式API来操作集合，Collection接口提供了一个stream()默认方法，用于返回该集合对应的流。

## Set集合

Set集合无序，不允许重复。如果试图把两个相同的元素加入到同一个Set集合中，则添加失败，add()方法返回false,且新元素不会被加入。

##### HashSet类

作为Set接口的典型实现，大多数时候使用Set集合时就是使用这个实现类。HashSet按Hash算法来存储集合中的元素，具有很好的存取和查找性能。具有以下特点：

* 不能保证元素的排列顺序；
* HashSet不是同步的，多个线程访问修改，必需通过代码来保证其同步；
* 集合元素可以是null；

如果两个元素通过equals()方法比较返回true，而hashCode()方法返回值不相等，HashSet会将它们存储在不同的位置，依然可以添加成功。【即HashSet集合判断两个元素相等的标准是两个对象通过equals()方法比较相等，并两个对象的hashCode()方法返回值也相等】

> 注：HashSet规则——如果两个对象通过equals()方法比较返回true，这两个对象的hashCode()值也应该相同。另，如果HashSet中两个以上的元素具有相同的hashCode值，将会导致性能下降。

当程序把可变对象添加到HashSet中之后，尽量不要去修改该集合元素中参与计算hashCode()、equals()的实例变量，否则将会导致HashSet无法正确操作这些集合元素。

##### LinkedHashSet类
LinkedHashSet是HashSet的子类，使用链表维护元素的次序，即当遍历LinkedHashMap集合里的元素时，LinkedHashSet将会按元素的添加顺序来访问集合里的元素。由于LinkedHashMap需要维护元素的插入顺序，因此性能略低于HashSet的性能，但在迭代访问Set里的全部元素时将会有很好的性能，因为它以链表来维护内部顺序。

##### TreeSet类
TreeSet是SortedSet接口的实现类，可以确保集合元素处于排序状态，与HashSet集合相比，TreeSet还提供了如下几个额外的方法：

* Compatator compatator():如果TreeSet采用了定制排序，则该方法返回定制排序所使用的Compatator；如果采用了自然排序，则返回null；
* Object first():返回集合中的第一个元素；
* Object last():返回集合中的最后一个元素；
* Object lower(Object o):返回集合中位于指定元素之前的元素（即下雨指定元素的最大元素，参考元素不需要是TreeSet集合里的元素）；
* Object higher(Object o):返回集合中位于指定元素之后的元素（即大雨指定元素的最小元素，参考元素不需要是TreeSet集合里的元素）；
* SortedSet subSet(Object fromElement,Object toElement):返回此Set子集合，范围从fromElement(包含)到toElement(不包含)；
* SortedSet headSet(Object toElement):返回此Set子集，由小于toElement的元素组成；
* SortedSet tailSet(Object fromElement):返回此Set的子集，由大于或等于fromElement的元素组成；

TreeSet采用红黑树的数据结构来存储集合元素，其支持两种排序方法：自然排序和定制排序。

1.自然排序

TreeSet会调用集合元素的compareTo(Object obj)方法来比较元素之间的大小关系，然后将集合元素按升序排列，这种方式就是自然排序。

Java的一些常用类已经实现了Comparable接口，并提供了比较大小的标准：

* BigDecimal、BigInteger以及所有的数值型对应的包装类：按它们对应的数值大小进行比较；
* Character：按字符的UNICODE值进行比较；
* Boolean：true对应的包装类实例大于false对应的包装类实例；
* String：按字符串中字符的UNICODE值进行比较；
* Date、Time：后面的时间、日期比前面的时间、日期大；

如果试图把一个对象添加到TreeSet时，则该对象的类必需实现Comparable接口，否则程序将会抛出异常；向TreeSet中添加的应该是同一个类的对象，否则会引发ClassCastException异常；对于TreeSet集合而言，它判断两个对象是否相等的唯一标准就是：两个对象通过compareTo(Object obj)方法比较是否返回0.

> 注：当需要把一个对象放入TreeSet中，重写该对象对应类的equals()方法时，应保证该方法与compareTo(Object obj)方法有一致的结果。

2.定制排序

TreeSet的自然排序是根据集合元素的大小，TreeSet将它们以升序排列。如果需要定制排序，例如以降序排列，则可以通过Comparator接口的帮助。该接口包含一个int compare(T o1,To2)方法，用于比较o1和o2的大小。

如果需要实现定制排序，则需要在创建TreeSet集合对戏那个时，提供一个Comparator对象与该TreeSet集合关联，由该Compatator对象负责集合元素的排序逻辑。由于Comparator是一个函数式接口，因此可以使用Lambda表达式来代替Comparator对象。

##### EnumSet
EnumSet是一个转为枚举类型设计的集合类，EnumSet中的所有元素都必需是指定枚举类型枚举值。EnumSet的集合元素也是有序的，以枚举值在Enum类内的定义顺序来决定集合元素的顺序。EnumSet不允许加入null元素。EnumSet类没有暴露任何构造器来创建该类的实例，程序通过类方法来创建对象。常用的类方法如下：

* EnumSet allOf(Class elementType):创建一个包含指定枚举类里所有枚举值的EnumSet集合；
* EnumSet complementOf(EnumSet s):创建一个其元素类型与指定EnumSet里元素类型相同的EnumSet集合，新的EnumSet集合包含有EnumSet集合所不包含的、此枚举类剩下的枚举值（即新的EnumSet集合和原EnumSet集合的集合元素加起来就是该枚举类的枚举值）；
* EnumSet copeOf(Collection c):使用一个普通集合来创建EnumSet集合；
* EnumSet copyOf(EnumSet s):创建一个与指定EnumSet具有相同元素类型、相同集合元素的EnumSet集合；
* EnumSet noneOf(Class elementType):创建一个元素类型为指定枚举类型的空EnumSet；
* EnumSet of(E first,E... rest):创建一个包含一个或多个枚举值的EnumSet集合，传入的多个枚举值必须属于统一个枚举类；
* EnumSet range(E from, E to):创建一个包含从from枚举值到to枚举值范围内所有枚举值的EnumSet；

##### 各Set实现类的性能分析

* HashSet与TreeSet作为Set的两个典型实现。HashSet的性能总比TreeSet好（特别是常用的添加、查询等操作），因为TreeSet需要额外的红黑树算法来维护集合元素的次序。只有当需要一个保持排序的Set时，蔡应该使用TreeSet，否则都应该使用HashSet；
* LinkedHashSet作为HashSet的子类，对于普通的插入、删除操作，LinkedHashSet比HashSet略慢，但是遍历时，LinkedHashSet会更快一点；
* EnumSet是所有Set实现类中性能最好的，但它只能保存同一个枚举类的枚举值作为集合元素；
* Set的三个实现类（HashSet、TreeSet、EnumSet）都是线程不安全的。通常可以通过Collections工具类的synchronizedSortedSet方法来“包装”该Set集合。

## List集合
List集合代表一个有序，可重复的集合，集合中每个元素都尤其对应的顺序索引，默认按照元素的添加顺序设置元素的索引。

##### Java 8改进的List接口和ListIterator接口

List除了使用Collection接口里的全部方法，还增加了一些根据索引来操作集合元素的方法：

* void add(int index,Object ele):将元素ele插入到List集合的index处；
* boolean addAll(int index,Collection c):将集合c所包含的所有元素都插入到List集合的index处；
* Object get(int index):返回集合index索引处的元素；
* index indexOf(Object o):返回对象o在集合List中第一次出现的位置索引；
* index lastIndexOf(Object o):返回对象o在集合List中最后一次出现的位置索引；
* Object remove(int idnex):删除并返回index索引处的元素；
* Object set(int index,Object ele):将index索引出的元素替换成ele对象，返回被替换的旧元素；【注：该方法不会改变List集合的长度，即index必须是有效索引】
* List subList(int fromIndex,int toIndex):返回从索引fromIndex（包含）到索引toIndex（不包含）处所有集合元素组成的子集合；

除此之外，Java 8还未List接口添加了如下两个默认方法：

* void replaceAll(UnaryOperator operator):根据operator指定的计算规则重新设置List集合的所有元素；
* void sort(Comparator c):根据Comparator参数对List集合的元素排序；

List判断连个对象相等只要通过equals()方法比较返回true即可。

ListIterator接口继承了Iterator接口，提供了专门操作List的方法，其增加了如下方法：

* boolean hasPrevious():返回该迭代器关联的集合是否还有上一个元素；
* Object previous():返回该迭代器的上一个元素；
* void add(Object o):在指定位置插入一个元素；

##### ArrayList和Vector实现类
ArrayList和Vector作为List类的两个典型实现，都是基于数组实现的List类，封装了一个动态的、允许再分配的Object[]数组。ArrayList或Vector对象使用initialCapacity参数来设置该数组的长度。如果创建空的ArrayList或Vector集合时不指定initialCapacity参数，则object
[]数组的长度默认是10，当添加元素时，超出了数组的长度，它们的initialCapacity会自动增加。

ArrayList和Vector提供了如下两个放荡分来冲洗能分配Object[]数组：

* void ensureCpacity(int minCapacity):将ArrayList或Vector集合的Object[]数组长度增加大于或等于minCapactiy值；
* void trimToSize():调整ArrayList或Vector集合的Object[]数组长度为当前元素的个数。调用该方法可减少ArrayList或Vector集合对象占用的存储空间。

ArrayList和Vector用法是几乎完全相同，Vector是一个古老的集合(JDK1.0就有了)。Vector的系列方法中方法名更短的方法属于后来新增的方法，方法名更长的方法则是Vector原有的方法。实际上，Vector有很多缺点，通常尽量少用Vector实现类。

ArrayList和Vector的显著区别是：ArrayList是线程不安全的，Vector集合则是线程安全的。正是因为此，Vector性能比ArrayList的性能要低。

Vector提供了一个Stack子类，用于模拟“栈”这种数据结构。Stack类提供了如下几个方法：

* Object peek():返回“栈”的第一个元素，但并不将钙元素“pop”出栈；
* Object pop():返回“栈”的第一个元素，并将该元素“pop”出栈；
* void push(Object item):将一个元素“push”进栈，最后一个进“栈”的元素总是位于“栈”顶；

由于Stack继承了Vector，也是一个古老的Java集合类，同样是线程安全的、性能较差的，因此也应该尽量少用Stack类。如果程序需要使用“栈”这种数据结构，则可以考虑使用后面将要介绍的ArrayDeque。

##### 固定长度的List
Arrays.ArrayList是一个固定长度的List集合，程序只能遍历访问该集合里的元素，不可增加、删除该集合里的元素。

## Queue集合
Queue用于模拟队列这种数据结构，通常不允许随机访问队列中的元素。Queue接口中定义了如下几个方法：

* void add(Object e):将指定元素加入此队列的尾部；
* Object element():获取队列头部的元素，但是不删除该元素；
* boolean offer(Object e):将指定元素加入此队列的尾部。当使用有容量限制的队列时，此方法通常比add(Object e)方法更好；
* Object peek():获取队列头部的元素，但是不删除该元素，如果队列为空，则返回null；
* Object poll():获取队列头部的元素，并删除该元素。如果队列为空，则返回null；
* Object remove():获取队列头部的元素，并删除该元素；

Queue接口有一个PriorityQueue实现类。除此之外，Queuehi阿尤一个Deque接口，Deque代表一个“双端队列”，因此Deque的实现类既可以当作队列使用，也可以当成栈使用。Java为Deque提供了ArrayDeque和LinkedList两个实现类。

##### PriorityQueue实现类
PriorityQueue是一个比较标准的队列实现类，其按队列元素的大小进行重新排序。不允许插入null元素，排序时两种方式：自然排序和定制排序。

##### Deque接口与ArrayDeque实现类
Deque接口是Queue接口的子接口，代表一个双端队列，定义了一些允许从两端来操作队列的元素的方法如下：

* void addFirst(Object e):将指定元素插入该双端队列的开头；
* void addLast(Object e):将指定元素插入到该双端队列的末尾；
* Iterator descendingIterator():返回该双端队列对应的迭代器，该迭代器将以逆向顺序来迭代队列中的元素；
* Object getFirst():获取但不删除双端队列的第一个元素；
* Object getLast():获取但不删除双端队列的最后一个元素；
* boolean offerFirst(Object e):将指定元素插入该双端队列的开头；
* boolean offerLast(Object e):将指定元素插入该双端队列的末尾；
* Object peekFirst():获取但不删除该双端队列的第一个元素，如果双端队列为空，返回null；
* Object peekLast():获取但不删除该双端队列的最后一个元素，如果双端队列为空，返回null；
* Object pollFirst():获取并删除该双端队列的第一个元素，如果双端队列为空，返回null；
* Object pollLast():获取并删除该双端队列的最后一个元素，如果双端队列为空，返回null；
* Object pop()(栈方法):pop出该双端队列所表示的栈顶元素。相当于removeFirst();
* void push(Object e)（栈方法）:将一个元素push进该双端队列所表示的栈的栈顶。相当于addFirst(e);
* Object removeFirst():获取并删除该双端队列的第一个元素；
* Object removeFirstOccurrence(Object o):删除该双端队列的第一次出现的元素o;
* Object removeLast():获取并删除该双端队列的最后一个元素；
* boolean removeLastOccurrence(Object o):删除该双端队列的最后一次出现的元素o；

ArrayDeque是Deque接口的典型实现类。它是基于数组的双端能队列，创建Deque时可指定一个numElements参数，该参数用于指定Object[]数组的长度；如果不指定numElements参数，Deque底层数组的长度为16；ArrayDeque既可以作为队列使用，也可以作为栈使用。

##### LinkedList实现类
LinkedList类是List接口的实现类，可根据索引来随机访问集合中的元素，又实现了Deque接口，可以被当成双端队列来使用。

LinkedList与ArrayList、ArrayDeque的实现机制完全不同，Arraylist、ArrayDeque内部以数组的形式来保存集合中的元素，因此随机访问集合元素式有较好的性能；而LinkedList内部以链表的形式来保存集合中的元素，因此随机访问集合时性能较差，但是插入、删除元素时性能比较出色。

##### 各种线性表的性能分析
Java提供的List就是一个线性表接口，而ArrayList、LinkedList又是线性表的两种典型实现，一种基于数组的线性表，一种基于链的线性表。Queue代表了队列，而Deque代表了双端队列。LinkedList集合不仅提供了List的功能，还提供了双端队列，栈的功能，尽管随机访问的性能不如ArrayList，但是在插入、删除操作时具有较好的性能。但总体来说，ArrayList的性能比LinkedList的性能要好，所以大部分时候应该考虑使用ArrayList。

关于使用List集合的建议：

* 遍历List集合元素，对于ArrayList、Vector集合，应该使用随机访问方法(get)遍历，性能更好；对于LinkedList集合，应该采用迭代器(Iterator)来遍历；
* 如果需要经常插入、删除操作来改变包含大量数据的List集合，考虑使用LinkedList集合；
* 如果有多个线程需要同时访问List集合中的元素，可以考虑使用collections将集合包装成线程安全的集合。

## Java 8增强的Map集合
Map用于保存具有映射关系的数据，Map集合里保存着两组值，一组值用于保存Map里的key，另外一组值用于保存Map里的value，key和value都可以是任何引用类型的数据。Map的key不允许重复，即同一个Map对象的任何两个key通过equals方法比较总是返回false。

Map与Set之间的关系非常密切，与Set接口下的子接口和实现类相似，Map接口下也有HashMap、LinkedHashMap、SortedMap、TreeMap、EnumMap等子接口和实现类。从Java源码来看，Java是先实现了Map，然后通过包装一个所有value都为null的Map就实现了Set集合。

Map有时被称之为字典，或关联数组。该接口定义了如下常用的方法：

* void clear():删除该Map对象中的所有key-value对；
* boolean containsKey(Object):查询Map中是否包含指定的key，如果包含则返回true；
* boolean containsValue(Objectsvalue):查询Map中是否包含一个或多value，如果包含则返回true；
* Set entrySet():返回Map中包含的key-value对所组成的Set集合，每个集合元素都是Map.Entry（Entry是Map的内部类）对象；
* Object get(Object key):返回指定key所对应的value；如果此Map中不包含该key，返回null；
* boolean isEmpty():查询该Map是否为空（即不包含任何key-value对），如果为空，则返回true；
* Set keySet():返回该Map中所有key组成的Set集合；
* Object put(Object key,Object value):添加一个key-value对，如果当前Map中已有一个与该key相等的key-value对，则新的key-value对会覆盖原来的key-value对；
* void putAll(Map m):将指定Map中的key-value对复制到本Map中；
* Object remove(Object key):删除指定key所对应的key-value对，返回被删除key所关联的value，如果该key不存在，则返回null；
* boolean remove(Object key,Object value):这是Java 8新增的方法，删除指定key、value所对应的key-value对，如果从该Map中成功地删除该key-value对，该方法返回true，否则返回false；
* int size():返回该Map里的key-value对的个数；
* Collection values():返回该Map里所有value组成的Collection；

Map中包括一个内部类Entry，该类封装了一个key-value对，Entry包含如下三个方法：

* Object getKey():返回该Entry里包含的key值；
* Object getValue():返回该Entry里包含的value值；
* Object setValue(V value):设置该Entry里包含的value值，并返回新设置的value值；

##### Java 8为Map新增的方法
Java 8为Map增加了remove(Object key,Object value)默认方法之外，还增加了如下方法：

* Object compute(Object key,BiFunction remappingFunction):该方法使用remappingFunction根据源key-value对计算一个新value，只要新alue部位null，就使用新value覆盖原value；如果原value部位null，新value为null，则删除原key-value对；如果原value、新value同时为null，那么该方法不改变任何key-value对，直接返回null；
* Object computeIfAbsent(Object key,Function mappingFunction):如果传给该方法的key参数在Map中对应的value为null，则使用mappingFunction根据key计算一个新的结果，如果计算结果不为null，则用计算结果覆盖原有的value，如果原Map原来不包括该key，那么该方法可能会添加一组key-value对；
* Object computeIfPresent(Object key,BiFunction remappingFunction):如果传给该方法的key参数在Map中对应的value不为null，该方法将使用remappingFunction根据原key、value计算一个新的结果，如果新的结果不为null，则使用该结果覆盖原来的value，如果计算结果为null，则删除原key-value对；
* void forEach(BiConsumer action):该方法是java 8为Map新增的一个遍历key-value对的方法，通过该方法可以更加简洁地遍历Map的key-value对；
* Object getOrDefault(Object key,V defaultValue):获取指定key对应的value，如果该key不存在，则返回defaultValue；
* Object merge(Object key,Object value,BiFunction remappingFunction):该方法会根据key参数获取该Map中对应的value。如果获取的value为null，则直接用传入的alue覆盖原有的value（在这种情况下，可能要添加一组key-value对）；如果获取的value不为null，则使用remappingFunction函数根据原value、新value计算一个新的结果，并用得到的结果去覆盖原有的value；
* Object putIfAbsent(Object key,Object value):该方法会自动检测指定key对应的alue是否为null，如果该key对应的value为null，该方法将会用新的value代替原来的null值；
* Object replace(Obejct key,Object value):将Map中指定key对应的value替换成新的value，与传统的put()方法不同的是，该方法不可能添加新的key-value对，如果尝试替换的key在原Map中不存在，该方法不会添加key-value对，而是返回null；
* boolean replace(K key,V oldValue,V newValue):将Map中指定key-value对的原value替换成新value。如果在Map中找到指定的key-value对，则执行替换并返回true，否则返回false;
* replaceAll(BiFunction function):该方法使用BiFunction对原key-value对执行计算，并将计算结果作为该key-value对的value值；

##### Java 8该机你的HashMap和Hashtable实现类
HashMap和Hashtable都是Map接口的典型实现类，它们之间的关系完全类似于ArrayList和Vector的关系：HashTable是一个古老的Map实现类，从JDK1.0就开始出现。Hashtable和HashMap存在亮点典型区别：

* Hashtable是一个线程安全的Map实现，但HashMap是线程不安全的实现，所以HashMap比Hashtable的性能高一点，所以如果有多个线程访问同一个Map对象时，使用Hashtable实现类会更好；
* Hashtable不允许使用null作为key和value，如果试图把null放进Hashtable中，将会引发NullPointerException异常；但HashMap可以使用null作为key或value；

HashMap、Hashtable也不能保证其中的key-value对的顺序，同时判断两个key是否相等的标准也是：两个key通过equals()方法还比较返回true，两个key的hashCode值也想等。HashMap、Hashtable判断两个value相等的标准是：只要两个对象通过equals()方法比较返回true即可。

> 注：尽量不要使用可变对象作为HashMap、Hashtable的key，如果确实需要使用可变对象作为HashMap、Hashtable的key，则尽量不要在程序中修改作为key的可变对象；

##### LinkedHashMap实现类
LinkedHashMap使用双向链表来维护key-value对的次序，该链表负责维护Map的迭代顺序，迭代顺序与key-value对的插入顺序保持一致。LinkedHashMap可以避免对HashMap、Hashtable里的key-value对进行排序，同时又可避免使用TreeMap所增加的成本。LinkedHashMap需要维护元素的插入顺序，因此性能略低于HashMap的性能，但因为它以链表来维护内部顺序，所以在迭代访问Map里的全部元素时将有较好的性能；

##### 使用Properties读写属性文件
Properties类是Hashtable类的子类，它可以把Map对象和属性文件关联起来，从而可以把Map对象中的key-value对写入属性文件中，也可以把属性文件中的“属性名=属性值”加载到Map对象中。Properties里的key、value都是字符串类型。该类提供了如下三个方法来修改Properties里的key、value值：

* String getProperty(String key):获取Properties中指定属性名对应的属性值，类似于Map的get(Object key)方法；
* String getProperty(String key,String defaultValue):该方法与前一个方法类似，该方法多一个功能，如果Properties中不存在指定的key值，则该方法指定默认值；
* Object setProperty(String key,String value):设置属性值，类似于Hashtable的put()方法；

除此之外，它还提供了来年哥哥读写属性文件的方法：

* void load(InputStream inStream):从属性文件（以输入流表示）中加载key-value对，把加载到的key-value对追加到Properties里（Properties是Hashtable的子类，它不保证key-value对之间的次序）；
* void store(OutputStream,String comments):将Properties中的key-value对输出到指定的属性文件（以输出流表示）中；

Properties可以把key-value对以XML文件的形式保存起来，也可以从XML文件中加载key-value对。

##### SortedMap接口和TreeMap实现类
TreeMap是一种红黑树数据结构，每个key-value对即作为红黑树的一个节点。TreeMap可以保证所有的key-value对处于有序状态。TreeMap也有两种排序方式：

* 自然排序：TreeMap的所有key必需实现Comparable接口，而且所有的key应该是同一个类的对象，否则将会抛出ClassCastException异常；
* 定制排序：创建TreeMap时，传入一个Comparator对象，该对象负责对TreeMap中的所有key进行排序。

TreeMap中判断两个key相等的标准是：两个key通过compareTo()方法返回0，TreeMap即认为这两个key是相等。

TreeMap也提供了一系列根据key顺序访问key-value对的方法：

* Map.Entry firstEntry():返回该Map中最小key所对应的key-value对，如果该Map为空，则返回null；
* Object firstKey():返回该Map中的最小key值，如果该Map为空，则返回null；
* Map.Entry lastEntry():返回该Map中最大key所对应的key-value对，如果该Map为空或不存在这样的key-value对，则都返回null；
* Object lastKey():返回该Map中的最大key值，如果该Map为空或不存在这样的key，则返回null；
* Map.Entry higherEntry(Object key):返回该Map中位于key后一位的key-value对（即大雨指定key的最小key所对应的key-value对）。如果该Map为空，则返回null；
* Object higherKey(Object key):返回该Map中位于key后一位的key值（即大雨指定key的最小key值）。如果该Map为空或不存在这样的key-value对，则都返回null；
* Map.Entry lowerEntry(Object key):返回该Map中位于key前一位的key-value对（即小于指定key的最大key所对应的key-value对）。如果该Map为空或不存在这样的key-value对，则都返回null；
* Object lowerKey(Object key):返回该Map中位于key前一位的key值（即小于指定key的最大key值）。如果该Map为空或不存在这样的key，则都返回null；
* NavigableMap subMap(Object fromKey,boolean fromInclusive,Object toKey,boolean toInclusive):返回该Map的子Map，其key的范围是从fromKey(是否包括取决于第二个参数)到toKey（是否包括取决于第四个参数）；
* SortedMap subMap(Object fromKey,Object toKey):返回该Map的子Map，其可以的范围是从fromKey(包括)到toKey(不包括);
* SortedMap tailMap(Obejct fromKey):返回该Map的子Map，其key的范围是大雨fromKey（包括）的所有key；
* SortedMap tailMap(Obejct fromKey,boolean inclusive):返回该Map的子Map，其key的范围是大雨fromKey（是否包括取决于第二个参数）的所有key；
* NavigableMap headMap(Object toKey):返回该Map的子Map，其key的范围是小于toKey(不包括)的所有key；
* NavigableMap headMap(Object toKey):返回该Map的子Map，其key的范围是小于toKey(是否包括取决于第二个参数)的所有key；

##### WeakhashMap实现类
WeakHashMap与HashMap的用法基本类似。与HashMap的区别在于，HashMap的key保留了对实际对象的强引用，这意味着只要该HashMap对象不被销毁，该HashMap的所有key所引用的对象就不会被垃圾回收，HashMap也不会自动删除这些key所对应的key-value对；但WeakHashMap的key只保留了对实际对象的若引用，这意味着如果WeakHashMap对象的key所引用的对戏那个没有被其他强引用变量所引用，则这些key所引用的对象可能被来及回收，WeakHashMap也可能自动删除这些key所对应的key-value对。

##### IdentityHashMap实现类
IdentityHashMap实现类的实现机制与HashMap基本相似，但它在处理两个key相等时比较独特：在IdentityHashMap中，当且进档两个key严格相等（key1==key2）时，IdentityHashMap才认为两个key相等；对于普通的HashMap而言，只要key1和key2通过equals()方法比较返回true，且它们的hashCode值相等即可。

##### EnumMap实现类
EnumMap是一个与枚举类一起使用的Map实现，EnumMap中的所有key都必需是单个枚举类的枚举值。创建EnumMap时必须显式或隐式指定它对应的枚举类。EnumMap具有如下特征：

* EnumMap在内部以数组形式保存，所以这种实现方式非常紧凑、高效；
* EnumMap根据key的自然顺序（即枚举值在枚举类中的定义顺序）来维护key-value对的顺序。
* EnumMap不允许使用null作为key，但允许使用null作为value。

创建EnumMap时必需指定一个枚举类，从而将该EnumMap和指定枚举类关联起来。

##### 各Map实现类的性能分析
* HashMap和Hashtable虽然实现机制几乎一样，但HashMap通常比Hashtable要快；
* TreeMap通常比HashMap和Hashtable要慢（尤其在插入、删除key-value对时更慢），因为TreeMap底层采用红黑树来管理key-value对；
* 使用TreeMap的好处是：TreeMap中的key-value对总是处于有序状态，无须专门进行排序操作。
* 对于一般的应用场景，程序应该多考虑使用HashMap，但程序若需要一个总是排好序的Map时，则可以考虑使用TreeMap。
* EnumMap的性能最好。

## HashSet和HashMap的性能选项
HashSet、HashMap的hash表包含如下属性：

* 容量（Capacity）:hash表中桶的数量；
* 初始化容量（init capacity）：创建hash表时桶的数量；
* 尺寸（size）：当前hash表中记录的数量；
* 负载因子(load factor):“size/capacity”。轻负载的hash表具有冲突少、适宜插入和查询的特点（但是使用Iterator迭代元素比较慢）。
* 负载极限：0-1的数值，决定了hash表的最大填满程度。当hash表中的负载因子达到指定的“负载极限”时，hash表会自动成倍地增加容量（桶的容量，并将原有的对象重新分配，放入新的桶内，这称为rehashing）。默认的负载极限是0.75.

## 操作集合的工具类：Collections
##### 排序操作
Collections提供了如下常用的类方法用于对List集合元素进行排序：

* void reverse(List list):反转指定List集合中元素的顺序；
* void shuffle(List list):对List集合元素进行随机排序；
* void sort(List list):根据元素的自然顺序对指定List集合的元素按升序进行排序；
* void sort(List list,Comparator c):根据指定Comparator产生的顺序对List集合元素进行排序；
* void swap(List list,int i,int j):将指定List集合中的i处元素和j处元素进行交换；
* void rotate(List list,int distance):当distance为正数时，将list集合的后distance个元素“整体”移到前面；当distance为负数时，将list集合的前distance个元素“整体”移到后面，该方法不改变集合的长度。

##### 查找、替换操作
Collections提供了如下常用的查找、替换集合元素的类方法：

* int binarySearch(List list,Object key):使用二分搜索指定的list集合，以获得指定对象在List集合中的所以。前提是List处于有序状态；
* Object max(Collection coll):根据元素的自然顺序，返回给定集合中的最大元素；
* Object max(Collection coll,Comparator comp):根据Comparator指定的顺序，返回给定集合中的最大元素；
* Object min(Collection coll):根据元素的自然排序，返回给定集合中的最小元素；
* Object min(Collection coll,Comparator comp):根据Comparator指定的排序，返回给定集合中的最小元素；
* void fill(List list,Object obj):使用指定元素obj替换指定List集合中的所有元素；
* int frequency(Collection coll,Object o):返回指定集合中指定元素的出现次数；
* int indexOfSubList(List source,List target):返回子List对象在父List对象中第一次出现的位置索引；如果符List中没有出现这样的子List，则返回-1；
* int lastIndexOfSubList(List source,List target):返回子List对象在父List对象中最后一次出现的位置索引；如果符List中没有出现这样的子List，则返回-1；
* boolean replaceAll(List list,Object oldVal,Object newVal):使用一个新值newVal替换List对象所有旧值oldVal；

##### 同步控制
Collections类中提供了多个synchronizedXxx()方法，该方法可以将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题。

Java中常用的集合框架中的实现类HashSet、TreeSet、ArrayList、ArrayDeque、LinkedList、HashMap、TreeMap都是线程不安全。

##### 设置不可变集合
Collections类提供了如下三类方法来返回一个不可变的集合：

* emptyXxx():返回一个空的、不可变的集合对象，此处的集合既可以是List，也可以是SortedSet、Set，还可以是Map、SortedMap等；
* singletonXxx():返回一个只包含指定对象（只有一个或一项元素）的、不可变的集合对象，此处的集合既可以是List、还可以是Map；
* unmodifiableXxx():返回指定集合对象的不可变视图，此处的集合既可以是List、也可以是Set、SortedSet，还可以是Map、SortedMap等；

##### 烦琐的接口：Enumeration
Enumeration接口是Iterator迭代器的“古老版本”，其只有两个名字很长的方法：

* boolean hasMoreElements():如果此迭代器还有剩下的元素，返回true；
* Object nextElement():返回迭代器的下一个元素，如果还有的话（否则抛出异常）；










 
