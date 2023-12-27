
## java基础

### 主类&程序入口

- 文件中必须存在与文件名相同的类
- 入口函数`public static void main(String[] args){}`
  - 多个类都可以有main，但是只有主类的会被自动调用

- 每个java文件中只能有一个public类，也只有这一个类可以在包外访问
  - 即其他的类适用于支撑唯一的public类的

### [[基本数据类型与包装类]]

### [[数组类型]]

### [[枚举类型]]

### [[字符串]]

### [[数组类型]]

### [[拷贝与引用]]

### 补充

- 自动类型推断（JDK11）
  - 使用`var`关键字
  - 必须为变量提供初始值才能进行推断
  - 不允许作为返回值

- Java不允许重复定义变量（即使是在内层作用域），也就是不能在内层重新定义并隐藏外层

- 比较：
  - 对于基本类型直接使用`= !=`
  - 对于非基本类型使用`=`会直接比较**引用**是否相同
  - 内置类型可以直接使用`equals()`进行比较
  - 自定义类型`equals()`也是比较引用，需要重写方法

- 位移
  - 数学位移`>>`
  - 逻辑位移`>>>`

 - 向上转型会丢失特定**类型的信息**，通过向下转型可以重新获取类型信息
- java中所有的转型都会被检查，运行时会检查强制转型是否正确（会抛出异常），这也是Java反射的一部分

### 流程控制

- for-in
``` java
for(type element: array)
{
    System.out.println(element);
}
```

- break标签
  - 标签应该放置在迭代语句之前
``` java
label:
xxx{
    {
        break label;
    }
}
```
- 控制break、continue控制**特定的迭代**（比如直接从内层break到最外层）
- 应用于嵌套循环

### [[switch]]

### 方法

- `public static void(){}`静态方法是类方法，可以通过**类名**直接调用(如main方法)，不能被子类重写，但可以被同名函数覆盖

- 静态代码块，内部全部为静态
``` java
static{

}
```
- 静态方法不能访问实例变量或实例方法，只能**访问静态变量和其他静态方法**。

- 方法与方法之间是平级关系，不能嵌套定义

- 可变参数
  - 必须是最后一个参数` public static void printMax( double... numbers)`使用数组类型也可以
  - 可以将传入的多个参数自动**转化为一个数组**
  - 允许传入0个元素

## 面向对象

### 思想

- 数据操作分离：实体类只负**责数据存取**（get/set），而对数据的处理交给其他类来完成，以实现**数据和数据业务**处理相分离

### 访问权限控制

- public
- private：仅类内访问
- default：包内可见（即不添加任何修饰符）
- protected：子类可访问，即使不在一个包，同时也提供了包访问权限
- 在只有包访问权限的类中使用public构造方法是没有意义的
- 类的访问权限
  - 类中可以不存在public类，此时文件可以随意命名
  - 只有默认（包），和public两种作用域

### [[构造方法]]

### [[jvm#垃圾回收|垃圾回收OC]]

- finalize(**已经弃用**)
  - 在对象被垃圾收集器析构(回收)之前调用，并且会在下一次垃圾收集时再回收这个对象占用的内存（**但也有可能不会被垃圾收集，比如内存没有被耗尽**,也就是说可能根本不会被调用）
```
protected void finalize()
{
   // 在这里终结代码
}
```

### 继承

- `public class Zi extends Fu{} `
- 多态
  - 对象多态 : 将方法的形参定义为父类类型, 这个方法可以接收该父类的任意子类对象 
  - 行为多态 : 同一个行为, 具有多个不同表现形式或形态的能力
  - 多态的好处：提高了程序的扩展性
  - 多态的弊端：不能使用子类的特有成员、

-  **不应该在构造函数中使用动态绑定**：构造函数被调用时子类还没有完全初始化，此时通过动态绑定去调用子类的方法可能存在问题（甚至成员变量都还没有赋初值进行初始化！
- 子类中重写方法的返回值可以是基类方法返回值的子类型

### 抽象类与抽象方法

- 抽象方法：`public abstract void`

- 抽象类：存在抽象方法的类必须**声明为抽象类**
``` java
public abstract class Fu {
    public abstract void 行善();
}
```
- 抽象类不能实例化，抽象类的子类要么重写抽象类中所有抽象方法，要么也是抽象类
### ![[final]]

### 组合与聚合

- **组合**强合成：“部分”的生命期不能比“整体”还要长
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115727663.png" alt="image-20230921115727663" style="zoom:50%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115826475.png" alt="image-20230921115826475" style="zoom: 50%;" />
- **聚合**、关联弱合成：部分可以独立存在
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115848748.png" alt="image-20230921115848748" style="zoom:50%;" />

### 重写运算

- 比较
``` java
@Override
    public boolean equals(Object obj) {
        // 检查是否为同一个对象的引用
        if (this == obj) {
            return true;
        }

        // 检查是否是正确的类型
        if (obj == null || getClass() != obj.getClass()) {
            return false;
        }

        // 类型转换
        Person person = (Person) obj;

        // 对比所有相关字段
        return age == person.age &&
               (name != null ? name.equals(person.name) : person.name == null);
    }

    @Override
    public int hashCode() {
        // 生成一个合理的 hashCode
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }
```
- **重写 `hashCode`**：重写 `equals` 方法时，也应该重写 `hashCode` 方法，以保持 `hashCode` 与 `equals` 的一致性。如果两个对象通过 `equals` 方法比较相等，那么这两个对象调用 `hashCode` 方法必须产生相同的整数结果。(对于使用基于**散列**的集合类（如 `HashSet` 和 `HashMap`）尤为重要)

### 委托

- java没有直接提供，需要手动实现
- 将成员对象放在新类中，并在新类中公开
- 如飞船继承发动机并不合理，因此使用组合并公开方法（通过组合的方式间接实现委托）
``` java
public class SpaceShipControls {
  void up(int velocity) {}
  void down(int velocity) {}
  void left(int velocity) {}
  void right(int velocity) {}
  void forward(int velocity) {}
  void back(int velocity) {}
  void turboBoost() {}
}
public class SpaceShipDelegation {
  private String name;
  private SpaceShipControls controls =
    new SpaceShipControls();
  public SpaceShipDelegation(String name) {
    this.name = name;
  }
  // Delegated methods:
  public void back(int velocity) {
    controls.back(velocity);
  }
  public void down(int velocity) {
    controls.down(velocity);
  }
  public void forward(int velocity) {
    controls.forward(velocity);
  }
  public void left(int velocity) {
    controls.left(velocity);
  }
  public void right(int velocity) {
    controls.right(velocity);
  }
  public void turboBoost() {
    controls.turboBoost();
  }
  public void up(int velocity) {
    controls.up(velocity);
  }
  public static void main(String [] args) {
    SpaceShipDelegation protector =
      new SpaceShipDelegation("NSEA Protector");
    protector.forward(100);
  }
}
```

### [[接口]]

### [[内部类]]

## [[泛型]]

## [[反射]]

## [[异常]]

## [[测试]]

## 并发编程

### [[基本概念]]

### [[线程与线程池]]

### [[线程安全的数据类型]]

### [[高级#任务调度|任务调度]]

## I/O

### [[控制台IO]]

### [[旧IO]]

### [[NIO]]

### [[文件]]

### [[序列化与反序列化]]

### [[网络编程]]

## java.util

### 集合

- 长度可变，更多功能

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231112215809676.png" alt="image-20231112215809676" style="zoom:33%;" />

- `Collection`是**一个接口**，是Java集合框架的一部分。它提供了用于操作一组对象的基本方法，如添加、删除、遍历等。`Collection`接口是多个集合类的超接口，包括`List`、`Set`、`Queue`等。

  - 是所有序列集合共同的**根接口**，存在一个默认实现AbstractCollection，可以通过继承来实现接口

  - 使用Collection作为参数可以实现一种通用的处理


``` java
public class CollectionSequence
extends AbstractCollection <Pet> {
  private Pet [] pets = new PetCreator().array(8);
  @Override
  public int size() { return pets.length; }
  @Override public Iterator <Pet> iterator() {
    return new Iterator <Pet>() {              // 还要实现迭代器接口
      private int index = 0;
      @Override public boolean hasNext() {
        return index < pets.length;
      }
      @Override
      public Pet next() { return pets [index++]; }
      @Override
      public void remove() { // Not implemented
        throw new UnsupportedOperationException();
      }
    };
  }
  public static void main(String [] args) {
    CollectionSequence c = new CollectionSequence();
    InterfaceVsIterator.display(c);
    InterfaceVsIterator.display(c.iterator());
  }
}
//由于 Collection 包含了 Iterator，因此如果一个类不是 Collection 的外部类时，实现 iterator 并作为通用的访问是更简单的方式
public class NonCollectionSequence extends PetSequence {
  public Iterator <Pet> iterator() {
    return new Iterator <Pet>() {
      private int index = 0;
      @Override public boolean hasNext() {
        return index < pets.length;
      }
      @Override
      public Pet next() { return pets [index++]; }
      @Override
      public void remove() { // Not implemented
        throw new UnsupportedOperationException();
      }
    };
  }
  public static void main(String [] args) {
    NonCollectionSequence nc =
      new NonCollectionSequence();
    InterfaceVsIterator.display(nc.iterator());
  }
}
```

- `Collections`是一个包含**静态方法的工具类**，这些方法用于操作或返回集合。它为整个集合框架提供了一系列的静态方法，如排序、搜索、线程安全转换等。

- 集合中不能使用基本数据类型

#### 基本方法

- 集合都需要引入，如`import java.util.ArrayList;`
  - `import java.util.*`

- 创建，如`ArrayList<String> sites = new ArrayList<String>();`

- 添加元素`add([index],item)`

- （按照下标）访问`get(下标)`

- 修改元素`set(下标,item)`

- 删除元素`remove(下标/引用)`

- 大小`size()`

- 清空`clear()`

- 复制`clone()`

- 查找`indexOf() lastIndexOf()`

- 删除位于指定集合内的元素`removeAll()`

- 添加一组元素
  - `list2.addAll([Index, ]list1);`
  - 所有Collection都具有的方法,只接受另一个Collection作为参数

- 打印集合
  - 可以直接`println`
  - 默认的打印是通过集合的`toString`方法提供的

- 创建不可修改的Collection或map

  - Collrctions接受一个原始集合返回一个只读版本


``` java
Collections.unmodifiableCollection(
       new ArrayList <>(data));
List <String> a = Collections.unmodifiableList(
        new ArrayList <>(data));
 Set <String> s = Collections.unmodifiableSet(
      new HashSet <>(data));
 Set <String> ss = Collections.unmodifiableSortedSet(
        new TreeSet <>(data));
Map <String,String> m = Collections.unmodifiableMap(
        new HashMap <>(Countries.capitals(6)));
Map <String,String> sm = Collections.unmodifiableSortedMap(
        new TreeMap <>(Countries.capitals(6)));
```

- 调用修改集合内容的方法会触发UnsupportedOperationException

- synchronized创建同步版本的集合

  - `synchronizedCollection(Collection<T> c)`: 返回指定集合的同步（线程安全）版本。
  - `synchronizedList(List<T> list)`: 返回指定列表的同步（线程安全）版本。
  - `synchronizedMap(Map<K,V> m)`: 返回指定映射的同步（线程安全）版本。
  - `synchronizedSet(Set<T> s)`: 返回指定集合的同步（线程安全）版本。


#### Iterator

- 迭代器可以用于**对不同集合**进行**通用的处理**

  - 只输入一个迭代器就可以对集合元素进行一系列的操作，迭代器统一了对集合的访问
  - `Iterator<Pet>it`迭代器类型
  - `Iterable<Pet>ip`可以产生特定类型迭代器的对象

- 是一种轻量级对象，创建成本很低

- 只能向一个方向移动

- 任何实现了Iterable接口的类都可以使用for-in循环

  - 数组、Map没有实现

- 基本操作

  - 创建`集合对象.iterator()`会创建一个指向第一个元素的迭代器
  - 使用`next()`获取下一个对象
  - 使用`hasNext()`检查是否还有更多对象
  - 使用`remove()`删除迭代器最近返回的元素


``` java
List <Pet> pets = new PetCreator().list(12);
Iterator <Pet> it = pets.iterator();
while(it.hasNext()) {
    Pet p = it.next();
    System.out.print(p.id() + ":" + p + " ");
}
//删除元素
for(int i = 0; i < 6; i++) {
      it.next();
      it.remove();
    }
//更简单的遍历方式
for(Pet p : pets)
      System.out.print(p.id() + ":" + p + " ");
```

- 自定义迭代器并实现多种顺序的for-in访问


``` java
public class MultiIterableClass extends IterableClass {
  public Iterable <String> reversed() {
    return new Iterable <String>() {
        //自定义逆向迭代器
      public Iterator <String> iterator() {
        return new Iterator <String>() {
          int current = words.length - 1;
          @Override public boolean hasNext() {
            return current > -1;
          }
          @Override public String next() {
            return words [current--];
          }
          @Override
          public void remove() { // Not implemented
            throw new UnsupportedOperationException();
          }
        };
      }
    };
  }
  public Iterable <String> randomized() {
    return new Iterable <String>() {
      public Iterator <String> iterator() {
        List <String> shuffled =
          new ArrayList <>(Arrays.asList(words));
        Collections.shuffle(shuffled, new Random(47));
          //不会影响原始数组，只是影响引用的顺序
        return shuffled.iterator();
      }
    };
  }
  public static void main(String [] args) {
    MultiIterableClass mic = new MultiIterableClass();
    for(String s : mic.reversed())
      System.out.print(s + " ");
    System.out.println();
    for(String s : mic.randomized())
      System.out.print(s + " ");
    System.out.println();
    for(String s : mic)
      System.out.print(s + " ");
  }
}
```

- 

##### ListIterator

- Iterator的更加强大的子类型，List类集合独有的
- 通过`.listIterator([index])`获取一个指向索引为index运算的迭代器
- 前后元素的索引`it.nextIndex() it.previousIndex()`
- 双向移动`.previous() .next()`
- `hasNext()`、`hasPrevious()`
- `.set(xx)`替换访问的最后一个元素

#### Collections库

- 排序和混排

  - **sort (List\<T> list)**: 对指定列表按自然顺序进行排序。
  - **sort(List\<T> list, Comparator\<? super T> c)**: 根据提供的比较器对列表进行排序。


``` java
Comparator <Person> ageComparator = new Comparator <Person>() {
    @Override
    public int compare(Person p1, Person p2) {
        return Integer.compare(p1.getAge(), p2.getAge());
    }
};
```

- **shuffle(List\<?> list)**: 随机打乱指定列表中元素的顺序。

- **shuffle(List\<?> list, Random rnd)**: 使用指定的随机源随机打乱列表。

- 查找和替换

  - **binarySearch(List\<? extends Comparable\<? super T>> list, T key)**: 使用二分查找算法在有序列表中查找指定对象。

  - **binarySearch(List\<? extends T> list, T key, Comparator\<? super T> c)**: 使用比较器进行二分查找。

  - **max(Collection\<? extends T> coll)**: 返回给定集合的最大元素。

  - **min(Collection\<? extends T> coll)**: 返回给定集合的最小元素。

  - **replaceAll(List\<T> list, T oldVal, T newVal)**: 替换列表中所有出现的指定值。

  - **`frequency(Collection, Object x)`**返回和x等价的元素数目

- 反转和旋转

  - **reverse(List\<?> list)**: 反转指定列表中元素的顺序。

  - **rotate(List\<?> list, int distance)**: 将列表中的元素旋转指定的距离。

- 集合的视图和包装

  - **unmodifiableCollection(Collection\<? extends T> c)**: 返回指定集合的不可修改视图。

  - **synchronizedCollection(Collection\<T> c)**: 返回指定集合的线程安全视图。

  - **singleton(T o)**: 返回只包含指定对象的不可变集合。

  - **emptyList()**, **emptySet()**, **emptyMap()**: 返回空的不可变列表、集合或映射。

- 复制和填充

  - **copy(List\<? super T> dest, List\<? extends T> src)**: 将所有元素从一个列表复制到另一个列表。

  - **fill(List\<? super T> list, T obj)**: 使用指定元素替换列表中的所有元素。只能用于 list 但是生成的列表可以用于构造器或 addAll
    - 只能替换已有的元素不能添加新元素

  - **`Collections.nCopies(n,item)`**创建具有相同元素的list

- 添加和移除

  - **addAll(Collection\<? super T> c, T... elements)**: 将所有指定元素添加到指定集合。

  - **disjoint(Collection\<?> c1, Collection\<?> c2)**: 如果两个指定集合没有共同的元素，则返回 true。

  - `removeIf(s->xx)`填入一个表达式，删除满足条件的元素

#### List

- List承诺以特定的顺序维护元素，支持在List中间插入和删除元素
- 使用`contains()`确认对象是否在列表中
- 切片`subList(int fromIndex, int toIndex);`，不包含`toIndex`
- `containsAll(Collection<?> c);`是否包含全部元素
- 求交`list.retainAll(retainElements);`,会对list进行原地修改

##### ArrayList

- 默认情况下存入的时Object对象`xx = new ArrayList();	
  - 明确类型`ArrayList<xx> xx = new ArrayList<>();`
  - 更简洁的写法`var apples = new ArrayList<xx>();`

- 可以向上转型为接口使用`List<Apple>apples = new ArrayList/LinkedList<>();`
- 添加一组元素
  - `new ArrayList<>(Arrays.asList(1,2,3...))`
    - 也可以直接传入一个数组                                            
    - 这种方法的底层是数组，后续不能添加/删除元素

- 转化为数组`.toArray()`
  - 默认会返回一个Object数组
  - 如果传递一个目标类那个的数组。就会根据这个类型生成
    - `pets.toArray(new Pet[0]);`


##### LinkedList

- 实现了queue\deque等接口，可以作为队列使用
- 使用`add`默认在末尾加，也可以指定`addFirst()/addLast()`
  - remove、get同理
  - 也可以随机访问，不过如果访问元素不在收尾，效率很低

###### Stack

- 使用ArrayDeque实现栈的方法，但必须声明为Deque类型
  - `Deque<String> stack = new ArrayDeque<>();`
- `.push(T item)`
- `.peek()`返回栈顶元素
- `.pop()`移除并返回栈顶元素

###### Queue

- `element()`返回队首元素，null时会报错
  - `peek()`可以返回null
- `offer()`在队尾插入元素
- `remove()`返回队尾元素，null时会报错
  - `poll()`可以返回null
- 虽然来源于LinkedList，但不能直接使用其方法

###### Deque

- Deque<\T>是一个接口
  - `Deque<String> deque = new linkedList<>();`
- `addFirst(E e)` / `offerFirst(E e)`: 在队列的头部插入元素。`addFirst`在空间不足时抛出异常，而`offerFirst`则返回`false`。
- `addLast(E e)` / `offerLast(E e)`: 在队列的尾部插入元素。`addLast`在空间不足时抛出异常，而`offerLast`则返回`false`。
- `removeFirst()` / `pollFirst()`: 移除并返回队列头部的元素。`removeFirst`在队列为空时抛出异常，而`pollFirst`则返回`null`。
- `removeLast()` / `pollLast()`: 移除并返回队列尾部的元素。`removeLast`在队列为空时抛出异常，而`pollLast`则返回`null`。
- `getFirst()` / `peekFirst()`: 返回队列头部的元素但不移除。`getFirst`在队列为空时抛出异常，而`peekFirst`则返回`null`。
- `getLast()` / `peekLast()`: 返回队列尾部的元素但不移除。`getLast`在队列为空时抛出异常，而`peekLast`则返回`null`。

#### PriorityQueue

- 基本操作与Queue一致，但是是优先队列
- 默认是小根堆
- 可以传入比较器，或者类型实现Comporable<>接口

#### Set

##### HashSet

- 不允许有重复元素，HashSet **是无序的**，即不会记录插入的顺序
- 使用`contains()`判断元素是否存在
- 允许存储一个null
- 范围搜索
  - 最小元素：`first()`
  - 最大元素：`last()`
  - `subSet(E fromElement, E toElement)`：获取`TreeSet`的一个子集，该子集包含从`fromElement`（包含）到`toElement`（不包含）的所有元素。（Element不一定要在集合中，只是表示一个范围）
  - `headSet(E toElement)`方法返回一个视图，包含小于（不包含）`toElement`的所有元素。
  - `tailSet(E fromElement)`方法返回一个视图，包含大于或等于`fromElement`的所有元素。
  - 注意对视图的修改是对原集合的修改
- 自定义数据类型要实现equals、hashCode


##### TreeSet

- 允许存储null

- 使用红黑树实现元素排序存储
- 创建时可以传入一个比较器，如`String.CASE_INSENSITIVE_ORDER`
- 自定义数据类型要实现equals、Comparable<>接口
- `last()``first()`生成最大/小元素
- 范围选择
  - `subSet(from,to)`不包含to
  - `headSet(to)`所有小于to
  - `taulSet(from)`所有大于等于from


##### LinkedHashSet

- 允许存储null

- 保持元素的**插入顺序**，迭代时将按照元素的添加顺序返回。
- 性能略低于`HashSet`，但在迭代访问整个集合时有更好的性能。

#### Map

##### HashMap

- 添加`put(,)`
- 对键、值分别查找`containsKey() containsValue()`
- 创建`Map<Integer, Integer>m = new HasMap<>();`

``` java
Integer freq = m.get(r);
m.put(r, freq==null?1: freq+1);
```

- 允许`null`键和`null`值。
- 不保证映射的顺序，顺序可能随时间发生变化。
- `entrySet()`生成Map.Entry（Map中的键值对）组成的Set，这个Set可以转化为流对象进行操作

##### TreeMap

- 不允许`null`键（如果使用自然顺序），但允许`null`值。

- 提供了一个有序的映射。

- 基于红黑树实现

- 常用方法


``` java
//小于等于 n 的最大键值（向前）
floorEntry(n);
//小于
lowerEntry(n);
//大于等于的（向后）
ceilingEntry();
//大于
higherEntry(K key)；
//首尾元素
firstEntry();
lastEntry();
pollFirstEntry();
pollLastEntry();
//逆序排序
map = map.descendingMap();
//范围查找元素

```


- 范围操作
  - `subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive)`:
    - 返回一个视图，包含从`fromKey`到`toKey`范围内的所有键值对，根据`fromInclusive`和`toInclusive`标志确定是否包含边界键。
  - `headMap(K toKey, boolean inclusive)`:
    - 返回一个视图，包含小于（或等于，如果`inclusive`为`true`）`toKey`的所有键值对。
  - `tailMap(K fromKey, boolean inclusive)`:
    - 返回一个视图，包含大于（或等于，如果`inclusive`为`true`）`fromKey`的所有键值对。

##### LinkedHashMap

- 基于哈希表和链表实现。
- 按插入顺序或访问顺序（构造时指定）**保持映射的顺序**。
- 允许`null`键和`null`值。

##### WeakHashMap

- 使用弱引用，可以减少存储空间占用
- 当一个键不再被使用时就会被垃圾清理自动处理

#### equals()与hashCode()

##### equals()

- 使用哈希集合需要自定义`equals()`和`hashCode()`

- Object默认的equals：

  - 比较引用，只有是同一个对象才返回true
  - 包装数据类型默认比较值而不是引用

- equals应该有的性质：自反性、对称性、传递性、一致性，与null比较应该返回flase

- Objects提供了一些使用方法

  - `Objects.equals(obj1, obj2);`对null安全的比较
    - 首先检查两个对象引用是否相同
    - 然后检查其中一个对象是否为`null`
    - 最后使用`equals`方法比较两个对象
  - `Objects.hashCode(obj);`

- 经典的比较


``` java
@Override public boolean equals(Object rval) {
    //既确保不为 null，也确保类型相同
    return rval instanceof SuccinctEquality &&
        Objects.equals(i, ((SuccinctEquality)rval).i) &&
        Objects.equals(s, ((SuccinctEquality)rval).s) &&
        Objects.equals(d, ((SuccinctEquality)rval).d);
}
```

- 有继承的比较


``` java
@Override public boolean equals(Object rval) {
    return rval instanceof ComposedEquality &&
      super.equals(rval) &&//先比较基类
      Objects.equals(part,999
        ((ComposedEquality)rval).part);
  }
```

##### hashCode()

- 重写原则：
  - 一致性：如果在应用的执行过程中一个对象的等价信息没有被修改，那么对这个对象调用 `hashCode()` 方法多次应该始终返回相同的值。
  - 高效率，均匀分布
  - 等价对等：如果两个对象通过 `equals()` 方法比较是相等的，那么这两个对象的 `hashCode()` 方法必须产生相同的整数结果。

#### 记录record（JDK16）

- 作为set和map键的类必须定义`equals() hashCode()`

- 作为数据传输对象

- 使用record关键字时编译器会自动生成：

  - 不可变字段
  - 规范构造器
  - 每个元素都有的访问器方法
  - equals()
  - hashCode()
  - toString()

- 创建


``` java
recoed Employee(String name, int id){}
var bob = new Employee("",0);
```

- 会自动创建规范的构造器根据传入的参数进行初始化，recoed的字段要定义在头部

- 不能加入静态方法、字段和初始化器

- record内的方法只能读取字段，不能进行修改，定义在{}内

- record不能继承其他类，但是可以实现接口


``` java
interface Strar{
    double brightness();
    double density();
}

record RedDwarf(double brightness) implement Star{
    @Override public double density(){return 100.0;}
}
//record 会自动为 brightness 提供访问器，正好匹配了接口的要求
```

- 可以嵌套在类或方法中，并且隐含都是静态的

- 可以自定义一个紧凑构造器（没有参数列表）


``` java
Redwarf {
    //可以修改字段的初始化值(这不是对 final 继续进行修改，而是在对 final 进行初始化前对初始化值进行修改)
    //也可以对参数进行验证
}
```

- 如果想要使用带参构造器，构造器必须静秋额复制record的签名（括号内完全一致）并且必须在函数体中对字段进行初始化

#### 使用享元自定义Collecction和Map

- 享元模式：共享对象，用于减少应用程序创建的对象数量，以减少内存占用和提高性能。

- 创建一个只读数组


``` java
public class CountingIntegerList
extends AbstractList <Integer> {
  private int size;
  public CountingIntegerList() { size = 0; }
  public CountingIntegerList(int size) {
    this.size = size < 0 ? 0 : size;
  }
    //重写 get 及 set
    //并没有实际存储 list，取值时再计算，可以创建无限大的数据集
  @Override public Integer get(int index) {
    return index;
  }
  @Override public int size() { return size; }
  public static void main(String [] args) {
    List <Integer> cil =
      new CountingIntegerList(30);
    System.out.println(cil);
    System.out.println(cil.get(500));
  }
}
```

- 实现Map享元


``` java
public class CountMap
extends AbstractMap <Integer,String> {
  private int size;
  private static char [] chars =
    "ABCDEFGHIJKLMNOPQRSTUVWXYZ".toCharArray();
    //自动计算 value
  private static String value(int key) {
    return
      chars [key % chars.length] +
      Integer.toString(key / chars.length);
  }
  public CountMap(int size) {
    this.size = size < 0 ? 0 : size;
  }
  @Override public String get(Object key) {
    return value((Integer)key);
  }
  private static class Entry
  implements Map.Entry <Integer,String> {
    int index;
    Entry(int index) { this.index = index; }
    @Override   public boolean equals(Object o) {
      return o instanceof Entry &&
        Objects.equals(index, ((Entry)o).index);
    }
    @Override public Integer getKey() { return index; }
    @Override public String getValue() {
      return value(index);
    }
    @Override public String setValue(String value) {
      throw new UnsupportedOperationException();
    }
    @Override public int hashCode() {
      return Objects.hashCode(index);
    }
  }
  @Override
  public Set <Map.Entry<Integer,String> > entrySet() {
    // LinkedHashSet retains initialization order:
    return IntStream.range(0, size)
      .mapToObj(Entry:: new)
      .collect(Collectors
        .toCollection(LinkedHashSet:: new));
  }

```

### 时间类

-  Date封装当前的日期和时间
   - 默认使用当前日期和时间来初始化对象（否则为毫秒数）`Date(long millisec)`
   - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921003154413.png" style="zoom: 33%;" />
-  SimpleDateFormat 格式化日期
   - 允许你选择任何用户自定义日期时间格式来运行`SimpleDateFormat ft = new SimpleDateFormat ("yyyy-MM-dd hh:mm:ss");`
-  日期类Calendar
   - Calendar类是一个抽象类，在实际使用时实现特定的子类的对象，创建对象的过程对程序员来说是透明的，只需要使用getInstance方法创建即可。
   - `Calendar c = Calendar.getInstance();//默认是当前日期`
-  GregorianCalendar类
   - Calendar类实现了公历日历，GregorianCalendar是Calendar类的一个具体实现。
### [[Math类]]
### logging日志

- `java.util.logging`

- 创建Logger实例`Logger logger = Logger.getLogger(name)`

  - 可以传入一个string作为logger的标识

- 日志级别

  - `logger.setLevel()`
  - `Level.SEVERE`、`Level.WARNING`、`Level.INFO` 等。
  - 也可以在记录异常时作为第一个参数临时设置级别

- 记录日志

  -  `logger.info()`、`logger.warning()`、`logger.severe()` 
  - 附带异常对象及堆栈信息`logger.log(Level.SEVERE, "Exception message", e)`

- 将日志输出到文件

``` java
Logger logger = Logger.getLogger("com.example.MyClass");
FileHandler fh = new FileHandler("mylog.log");
logger.addHandler(fh);
SimpleFormatter formatter = new SimpleFormatter();
fh.setFormatter(formatter);
logger.info("This is an info message");
```

## 杂项

### [[注解]]

### [[函数式编程]]
### [[流|流Stream]]

### [[包]]

### [[Java编码规范]]

### 环境配置

- `java-version`正常但是找不到`javac`：
  - 新建->变量名“CLASSPATH”,变量值`“.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar”`

### [[Javadoc]]