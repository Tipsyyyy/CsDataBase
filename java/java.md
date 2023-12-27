
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

![[switch]]

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

### [[标准IO]]

#### 新NIO

- Buffer包含一些要写入或者刚读出的数据。在NIO库中，所有数据都是用Buffer处理的。在读取数据时，它是直接读到Buffer中的；在写入数据时，也是写入到Buffer中的。
- ByteBuffer是唯一和FileChannel通信的类型。在NIO中，所有的数据都是通过Channel处理的。它就像水管一样，是一个通道。数据可以从Channel读取到Buffer中，也可以从Buffer写入到Channel中。
  - `FileChannel` 是 Java NIO（New Input/Output）中的一个关键类，用于文件的读取、写入、映射和操作。它提供了一个与文件相关联的通道，并支持高效的文件处理。
  - 隧道通常绑定一个目标对象，**作为从文件读取到缓冲区、从缓冲区写入到文件的中间桥梁**
  
- 对比传统IO：
  - NIO基于缓冲区进行操作，数据的读写都是通过Buffer进行的，这使得处理速度更快。
  - NIO可以进行非阻塞的读写操作。

##### ByteBuffer与Channel

- 创建特定大小的一块字节缓冲区，放入取出数据（注意这是一个底层方法，不能放入取出对象）

- 参数

  - capacity：冲区能够容纳的数据元素的**最大数量**。这个容量在缓冲区创建时被设置，且不能被改变。
  - mark：可以通过 `Buffer` 类的 `mark()` 方法来设置。`mark` **保存了一个** `position` 的值，可以通过 `reset()` 方法恢复到这个 `position`。
  - position：`position` 是缓冲区中**下一个要被读写**的元素的索引。`position` 的初始值为0。
  - limit：`limit` 是**第一个不应该被读或写的数据**的索引（读取模式下为0）。`limit` 的值不能大于 `capacity`。
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231121132322512.png" alt="image-20231121132322512" style="zoom:50%;" />
  - 相关方法
    - **flip()**：从**写模式切换到读模式**。调用 `flip()` 后，`position` 被设回0，`limit` 被设置为之前的 `position` 值。
    - **clear()**：清除缓冲区，准备重新写数据。`position` 被设回0，`limit` 被设置为 `capacity`。
    - **rewind()**：重置 `position` 为0，可以重新读写缓冲区中的所有数据。
    - **reset()**：讲position移动到mark的位置，如果mark还没有被设置则会抛出异常

- 创建
  - 直接指定大小`ByteBuffer buff = ByteBuffer.allocate(BSIZE);`
  - 从数据创建`c.write(ByteBuffer.wrap("Some text ".getBytes()));`
  - 使用`put`放入数据：`buff.put(str.getBytes());`将数据写入缓冲区的当前位置，并将**位置向前移动**。如果缓冲区的剩余空间不足以容纳新的数据，那么`put`方法将抛出`BufferOverflowException`异常。

- `fc.read(buff);`写入到缓冲区

- `fc.write(buff);`从缓冲区读取

- 输出缓冲区的内容`System.out.println(buff.asCharBuffer());`

  - 直接这么输出是字节内容，没有解码

- 重新读取/写入（讲position设置为0）`rewind()`

- 读写文件

  - 三种通道：可读、可写、可读写

  - 通过`.getChannel()`**获取通道**


``` java
public class GetChannel {
  private static String name = "data.txt";
  private static final int BSIZE = 1024;
  public static void main(String [] args) {
    // Write a file:
    try(
      FileChannel fc = new FileOutputStream(name)
        .getChannel()
    ) {//通道只能和 ByteBuffer 对象交互
      fc.write(ByteBuffer
        .wrap("Some text ".getBytes()));//通过字节创建 ByteBuffer
        //通过隧道从缓冲区写入数据到文件
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
    // Add to the end of the file:
    try(
      FileChannel fc = new RandomAccessFile(
        name, "rw").getChannel()
    ) {
      fc.position(fc.size()); // Move to the end
      fc.write(ByteBuffer
        .wrap("Some more".getBytes()));
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
    // Read the file:
    try(
      FileChannel fc = new FileInputStream(name)
        .getChannel()
    ) {
      ByteBuffer buff = ByteBuffer.allocate(BSIZE);//创建特定大小
        //通过隧道将文件内容读取到缓冲区
      fc.read(buff);
        //设置为读模式
      buff.flip();
      while(buff.hasRemaining())
        System.out.write(buff.get());
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
    System.out.flush();
  }
}
```

- 连接通道的内置方法

  - `transferTo`方法将数据从**源通道传输到目标通道**。它接收三个参数：开始位置、要传输的最大字节数和目标通道。从开始位置开始，最多传输指定数量的字节到目标通道。
    - `Channelin.transferTo(0, Channelin.size(), Channelout);`
  - `transferFrom`方法将数据从源通道传输到目标通道。它接收三个参数：源通道、开始位置和要传输的最大字节数。从源通道中读取最多指定数量的字节，并将它们写入到目标通道的开始位置。
    - Channel`out.transferFrom(Channelin, 0, Channelin.size());`

- 字符集CharSet

  - `Charset.forName(String charsetName)` 方法获取指定字符集的 `Charset` 实例`Charset utf8 = Charset.forName("UTF-8");`
  - 将字符串转换为字节序列：`ByteBuffer buffer = utf8.encode("测试文本");`编码
  - 将字节序列转换为字符串：`String text = utf8.decode(buffer).toString();`解码
  - 获取系统使用的字符集名称：`String encoding =System.getProperty("file.encoding");`

- 非字节数据

  - 缓冲区是对字节流的操作（不含数据类型）

  - 存储到缓冲区的数据读取出来后是不能直接使用的

  - 对于字符数据，要么在字节**放入时进行编码**，要么在从缓冲区**取出时进行解码**


``` java
String encoding =
    System.getProperty("file.encoding");
//输出时解码
System.out.println("Decoded using " +
                   encoding + ": " + Charset.forName(encoding).decode(buff));
// Encode with something that prints:
try(
    FileChannel fc = new FileOutputStream(
        "data2.txt").getChannel()
) {
    //陷入到隧道是解码
    fc.write(ByteBuffer.wrap(
        "Some text".getBytes("UTF-16BE")));
} catch(IOException e) {
    throw new RuntimeException(e);
}
// Now try reading again:
buff.clear();
try(
    FileChannel fc = new FileInputStream(
        "data2.txt").getChannel()
) {
    fc.read(buff);
} catch(IOException e) {
    throw new RuntimeException(e);
}
buff.flip();
System.out.println(buff.asCharBuffer());
```

- 存储非字符数据的更简单方法

  - `getX()`X为类型名称


```java
// Store and read a char array:
bb.asCharBuffer().put("Howdy!");
char c;
while((c = bb.getChar()) != 0)//读取字符
    System.out.print(c + " ");
System.out.println();
bb.rewind();
// Store and read a short:
bb.asShortBuffer().put((short)471142);
System.out.println(bb.getShort());
bb.rewind();
// Store and read an int:
bb.asIntBuffer().put(99471142);
System.out.println(bb.getInt());
bb.rewind();
// Store and read a long:
bb.asLongBuffer().put(99471142);
System.out.println(bb.getLong());
bb.rewind();
// Store and read a float:
bb.asFloatBuffer().put(99471142);
System.out.println(bb.getFloat());
bb.rewind();
// Store and read a double:
bb.asDoubleBuffer().put(99471142);
System.out.println(bb.getDouble());
bb.rewind();
```


- 视图缓冲区
  - 透过某个特定基本类型的视角来看底层的缓冲区


``` java
public static void main(String [] args) {
  ByteBuffer bb = ByteBuffer.allocate(BSIZE);
  //看作 Int 缓冲区
  IntBuffer ib = bb.asIntBuffer();
  // Store an array of int:
  ib.put(new int []{ 11, 42, 47, 99, 143, 811, 1016 });
  // Absolute location read and write:
  System.out.println(ib.get(3));
  ib.put(3, 1811);
  // Setting a new limit before rewinding the buffer.
  ib.flip();
  while(ib.hasRemaining()) {
      int i = ib.get();
      System.out.println(i);
  }
}
```

- 其它基本类型也有类似的方法
- 可以将同一个字节序类解析为不同类型，同样的缓冲区数据，不一样的解析方式


- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231121131949514.png" alt="image-20231121131949514" style="zoom: 33%;" />


##### 内存映射文件

- 处理因为太大而无法完整加载到内存的文件，可以根据地址把文件当作一个很大的数组来进行访问


```java
public class LargeMappedFiles {
  static int length = 0x8000000; // 128 MB
  public static void
  main(String [] args) throws Exception {
    try(
      RandomAccessFile tdat =
        new RandomAccessFile("test.dat", "rw")
    ) {
      MappedByteBuffer out = tdat.getChannel().map(
        FileChannel.MapMode.READ_WRITE, 0, length);
      for(int i = 0; i < length; i++)
        out.put((byte)'x');
      System.out.println("Finished writing");
      for(int i = length/2; i < length/2 + 6; i++)
        System.out.print((char)out.get(i));
    }
  }
}
```

##### 文件加锁

- 对文件枷锁会对文件的访问操作加上同步处理，这样文件才能作为共享资源，由于文件并不一定在一个JVM被操作（也可能被系统进程等操作），因此Java的文件加锁直接映射到本地操作系统，对其它进程可见


``` java
public class FileLocking {
  public static void main(String [] args) {
    try(
      FileOutputStream fos =
        new FileOutputStream("file.txt");
        //加锁
      FileLock fl = fos.getChannel().tryLock()
    ) {
        //得到了锁
      if(fl != null) {
        System.out.println("Locked File");
        TimeUnit.MILLISECONDS.sleep(100);
          //释放锁
        fl.release();
        System.out.println("Released Lock");
      }
    } catch(IOException | InterruptedException e) {
      throw new RuntimeException(e);
    }
  }
}
```

- `lock()`是阻塞方法，一直等到获得锁，try指示尝试一下

  - 参数`long position, long size, bollean shared`
  - 表示锁定的开始位置及长度（锁定范围），最后表示是否是共享锁

- 部分加锁（用于文件映射及数据库等较大共用文件）


``` java
private static class LockAndModify extends Thread {
    private ByteBuffer buff;
    private int start, end;
    LockAndModify(ByteBuffer mbb, int start, int end) {
      this.start = start;
      this.end = end;
      mbb.limit(end);
      mbb.position(start);
      buff = mbb.slice();
      start();
    }
    @Override public void run() {
      try {
        // Exclusive lock with no overlap:
        FileLock fl = fc.lock(start, end, false);
        System.out.println(
          "Locked: "+ start +" to "+ end);
        // Perform modification:
        while(buff.position() < buff.limit() - 1)
          buff.put((byte)(buff.get() + 1));
        fl.release();
        System.out.println(
          "Released: " + start + " to " + end);
      } catch(IOException e) {
        throw new RuntimeException(e);
      }
    }
  }
```


### 文件(新)

#### 路径

- Path路径表示文件、目录的路径，可以在不同操作系统上工作

- 从String或URI对象创建路径

  - `Path p = Paths.get(xx)`
  - JDK11之后`Path.of(xx)`

- 使用String设置多级路径

  - `Paths.get("C:", "path", "to", "nowhere", "NoFile.txt")`

- 方法


``` java
p.isAbsolute();//是否为绝对路径
p.getFileName();//获取文件名称
p.getParent();//获取上级路径
p.getRoot();//获取最顶层路径（如 c:/）
p.toRealPath();//获取绝对路径（取决于操作系统环境）
p.normalize();//规范路径
URI u = p.toUri();
Path puri = Paths.get(u);
```

- 切割


``` java
for(int i = 0; i < p.getNameCount(); i++)
      System.out.println(p.getName(i));
```

- 也可以直接使用for-in循环遍历

- 注意对路径使用startsWith、endsWith要求是完整一项，如`xx.java`

- 遍历的内容不包含根路径

- 添加/删除路径

  - **relativize(Path other)**：`relativize` 方法用于构建**两个路径之间的相对路径。**如果你有两个路径 `A` 和 `B`，调用 `A.relativize(B)` 将返回一个路径，它从 `A` 导航到 `B` 的相对路径。它假设两个路径有一个共同的根，且不会在结果中包含根路径部分。
  - **resolve(Path other)**：`resolve` 方法用于将一个路径**附加到另一个路径的末尾**。如果你有两个路径 `A` 和 `B`，调用 `A.resolve(B)` 会创建一个合并了两个路径的新 `Path` 对象，如果 `B` 是绝对路径，**则直接**返回 `B`。

##### Files工具类

``` java
say("Exists", Files.exists(p));
say("Directory", Files.isDirectory(p));
say("Executable", Files.isExecutable(p));
say("Readable", Files.isReadable(p));
say("RegularFile", Files.isRegularFile(p));
say("Writable", Files.isWritable(p));
say("notExists", Files.notExists(p));
say("Hidden", Files.isHidden(p));
say("size", Files.size(p));
say("FileStore", Files.getFileStore(p));
say("LastModified: ", Files.getLastModifiedTime(p));
say("Owner", Files.getOwner(p));
say("ContentType", Files.probeContentType(p));
say("SymbolicLink", Files.isSymbolicLink(p));
```

- 遍历目录`Files.walkFileTree(Path, SimpleFileVisitor<Path>)`

  - 需要创建并传入一个（匿名类）SimpleFileVisitor\<Path>
    - `preVisitDirectory`：在访问目录之前调用。
    - `visitFile`：在访问文件时调用。
    - `visitFileFailed`：在文件访问失败时调用。
    - `postVisitDirectory`：在访问目录之后调用。

  - 示例：删除文件夹


``` java
public static void rmdir(Path dir)
  throws IOException {
    Files.walkFileTree(dir,
      new SimpleFileVisitor <Path>() {
      @Override public FileVisitResult
      visitFile(Path file, BasicFileAttributes attrs)
      throws IOException {
        Files.delete(file);
        return FileVisitResult.CONTINUE;
      }
      //文件夹内的文件都被删除了，可以删除目录了
      @Override public FileVisitResult
      postVisitDirectory(Path dir, IOException exc)
      throws IOException {
        Files.delete(dir);
        return FileVisitResult.CONTINUE;
      }
    });
  }
```

- 创建


``` java
Path newFilePath = Paths.get("newfile.txt");
Files.createFile(newFilePath);
//如果文件已存在，抛 FileAlreadyExistsException

Path newDirPath = Paths.get("newdir");
Files.createDirectory(newDirPath);

//创建多级目录
Path dirsPath = Paths.get("newdir/subdir");
Files.createDirectories(dirsPath);

//删除文件或目录
Path pathToDelete = Paths.get("fileOrDirToDelete");
Files.delete(pathToDelete);
//删除目录时，目录必须是空的，否则会抛出 DirectoryNotEmptyException
```

- Files.walk提供更简单的遍历方式

  - 返回`Stream<Path>`对象


``` java
Path start = Paths.get("/some/path");
try (Stream <Path> stream = Files.walk(start, 3)) { // 遍历深度为 3
    stream.forEach(System.out:: println); // 打印每个文件和目录的路径
}
```

``` java
//删除 txt 文件
static Path test = Paths.get("test");
  static void delTxtFiles() {
    try {
      Files.walk(test)
        .filter(f ->
          f.toString().endsWith(".txt"))
        .forEach(f -> {
          try {
            System.out.println("deleting " + f);
            Files.delete(f);
          } catch(IOException e) {
            throw new RuntimeException(e);
          }
        });
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
  }
```

#### 文件系统

- 获取文件系统对象`FileSystem fsys = FileSystems.getDefault();`

  - 也可以通过path、uri等获取

- 监听Path（文件目录的变化）

  - 注意只会检测目标目录下的变化（不含子目录）


``` java
try {
    WatchService watcher =
        FileSystems.getDefault().newWatchService();
    dir.register(watcher, ENTRY_DELETE);
    Executors.newSingleThreadExecutor().submit(() -> {
        try {
            WatchKey key = watcher.take();
            for(WatchEvent evt : key.pollEvents()) {
                System.out.println(
                    "evt.context(): " + evt.context() +
                    "\nevt.count(): " + evt.count() +
                    "\nevt.kind(): " + evt.kind());
                System.exit(0);
            }
        } catch(InterruptedException e) {
            return;
        }
    });
} catch(IOException e) {
    throw new RuntimeException(e);
}
```

- 查找文件

  - 使用PathMatcher

  - ``` java
    //创建匹配模式，**/表示所有子目录，*.表示任意文件名称
    PathMatcher matcher = FileSystems.getDefault()
        .getPathMatcher(" glob:**/*.{tmp, txt}");
    Files.walk(test)//从特定目录遍历所有路径
        .filter(matcher:: matches)//匹配路径
        .forEach(System.out:: println);
    ```

  - ``` java
    PathMatcher matcher2 = FileSystems.getDefault()
          .getPathMatcher(" glob:*.tmp ");
    Files.walk(test) // 只匹配文件名
          .filter(Files:: isRegularFile)
          .map(Path:: getFileName)//将路径映射为文件名，下面才能正常进行匹配
          .filter(matcher2:: matches)
          .forEach(System.out:: println);
    ```

#### 读写文件

- 读取整个文件
  - `Files.readAllLines(Path)`
  - 会返回一个`List<String>`(行划分)
  - 可以直接创建一个流`.stream()`便于操作
  - 是一次性将整个文件读取到内存，可能存在性能问题
- 逐行读取文件
  - 将文件变成由行组成的stream
  - 逐行读取，性能更好
  - `Files.lines(Path).skip...`
- 写入文件
  - 可以将实现了Iterable接口的对象写入文件
  - `Files.write(Path,comtent);`

### 序列化与反序列化

- 对象序列化为字节流&从字节流还原对象

- 一个实现了相应接口的类


```java
public class Employee implements Serializable {
    private static final long serialVersionUID = 1L;
    private transient Address address;
    private Person person;
    // setters and getters
    private void writeObject(ObjectOutputStream oos)  throws IOException {
        oos.defaultWriteObject();
        oos.writeObject(address.getHouseNumber());
    }

    private void readObject(ObjectInputStream ois) throws ClassNotFoundException, IOException {
        ois.defaultReadObject();
        Integer houseNumber = (Integer) ois.readObject();
        Address a = new Address();
        a.setHouseNumber(houseNumber);
        this.setAddress(a);
    }
}
public class Address { private int houseNumber; }
```

### 网络编程

- 直接通过url获取数据


```java
URL oracle = new URL("http://www.oracle.com/");
        BufferedReader in = new BufferedReader(
        new InputStreamReader(oracle.openStream()));
```

- 建立连接进行读写


```java
URL oracle = new URL("http://www.oracle.com/");
URLConnection yc = oracle.openConnection();
BufferedReader in = new BufferedReader(new InputStreamReader(
    yc.getInputStream()));
OutputStreamWriter out = new OutputStreamWriter( connection.getOutputStream());
```

#### nio

- socket：套接字是网络上运行的两个程序之间**双向通信链路**的一个端点。套接字绑定到端口号，以便 TCP 层可以识别数据要发送到的应用程序

  - 服务器保持等待，监听套接字以等待客户端发出连接请求。


```java
ServerSocket serverSocket = new ServerSocket(80));
```

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225015934586.png" alt="image-20231225015934586" style="zoom:33%;" />

- 客户端通过域名及端口连接到服务器

  - `Socket echoSocket = new Socket(hostName, portNumber); `
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225015329591.png" style="zoom:33%;" />

- 服务器接受连接`Socket clientSocket = serverSocket.accept(); `

  - 原先的套接字用于与客户端连接，其远端被设置为连接的客户端的信息，服务器还会创建一个新的套接字用于继续监听新的连接
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225015838769.png" alt="image-20231225015838769" style="zoom: 50%;" />

- 连接后服务器和客户端可以使用套接字继续宁数据与交互

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225020025833.png" alt="image-20231225020025833" style="zoom:33%;" />

- 但是这样性能开销很大！

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225020100938.png" alt="image-20231225020100938" style="zoom:33%;" />

- selector
  - 使用一个线程管理多个channel节省资源
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225014249680.png" alt="image-20231225014249680" style="zoom:33%;" />
  - 允许**单个线程检查多个通道**上的 I/O 事件。选择器可以检查通道是否准备好进行操作，例如读取和写入。
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225020142910.png" alt="image-20231225020142910" style="zoom:33%;" />

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

  - **sort(List<T> list)**: 对指定列表按自然顺序进行排序。

  - **sort(List<T> list, Comparator<? super T> c)**: 根据提供的比较器对列表进行排序。


``` java
Comparator <Person> ageComparator = new Comparator <Person>() {
    @Override
    public int compare(Person p1, Person p2) {
        return Integer.compare(p1.getAge(), p2.getAge());
    }
};
```

- **shuffle(List<?> list)**: 随机打乱指定列表中元素的顺序。

- **shuffle(List<?> list, Random rnd)**: 使用指定的随机源随机打乱列表。

- 查找和替换

  - **binarySearch(List<? extends Comparable<? super T>> list, T key)**: 使用二分查找算法在有序列表中查找指定对象。

  - **binarySearch(List<? extends T> list, T key, Comparator<? super T> c)**: 使用比较器进行二分查找。

  - **max(Collection<? extends T> coll)**: 返回给定集合的最大元素。

  - **min(Collection<? extends T> coll)**: 返回给定集合的最小元素。

  - **replaceAll(List<T> list, T oldVal, T newVal)**: 替换列表中所有出现的指定值。

  - **`frequency(Collection, Object x)`**返回和x等价的元素数目

- 反转和旋转

  - **reverse(List<?> list)**: 反转指定列表中元素的顺序。

  - **rotate(List<?> list, int distance)**: 将列表中的元素旋转指定的距离。

- 集合的视图和包装

  - **unmodifiableCollection(Collection<? extends T> c)**: 返回指定集合的不可修改视图。

  - **synchronizedCollection(Collection<T> c)**: 返回指定集合的线程安全视图。

  - **singleton(T o)**: 返回只包含指定对象的不可变集合。

  - **emptyList()**, **emptySet()**, **emptyMap()**: 返回空的不可变列表、集合或映射。

- 复制和填充

  - **copy(List<? super T> dest, List<? extends T> src)**: 将所有元素从一个列表复制到另一个列表。

  - **fill(List<? super T> list, T obj)**: 使用指定元素替换列表中的所有元素。只能用于list但是生成的列表可以用于构造器或addAll
    - 只能替换已有的元素不能添加新元素

  - **`Collections.nCopies(n,item)`**创建具有相同元素的list

- 添加和移除

  - **addAll(Collection<? super T> c, T... elements)**: 将所有指定元素添加到指定集合。

  - **disjoint(Collection<?> c1, Collection<?> c2)**: 如果两个指定集合没有共同的元素，则返回true。

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

### Object

- 所有的类，都直接或者间接的继承了 Object 类 ，也就是说所有类都继承了Object的方法，可以进行重写
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230920235956794.png" alt="image-20230920235956794" style="zoom:50%;" />
  - toString() 返回对象的内容信息，而不是地址信息
  - equals便子类自己来定制比较规则。

### Math

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921000139100.png" alt="image-20230921000139100" style="zoom: 50%;" />

- | [rint()](https://www.runoob.com/java/number-rint.html)   | 返回与参数最接近的整数。返回类型为double。              |
  | -------------------------------------------------------- | ------------------------------------------------------- |
  | [round()](https://www.runoob.com/java/number-round.html) | 四舍五入                                                |
  | [exp()](https://www.runoob.com/java/number-exp.html)     | 返回自然数底数e的参数次方。                             |
  | [ log()](https://www.runoob.com/java/number-log.html)    | [ sqrt()](https://www.runoob.com/java/number-sqrt.html) |
  | 三角函数                                                 | 弧度转化                                                |

  

### System

- System的功能是静态的，都是直接用类名调用即可
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921000221654.png" alt="image-20230921000221654" style="zoom: 50%;" />

### BigDecimal

- 解决小数运算中, 出现的不精确问题，高精计算
- 创建<img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921000358201.png" alt="image-20230921000358201" style="zoom:50%;" />
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921000427758.png" alt="image-20230921000427758" style="zoom:50%;" />

## 杂项

### 引用类型

- 强引用：Strong Reference
  - 默认情况下，当我们在Java中创建一个对象并赋值给一个变量时，就是创建了一个强引用。只要强引用存在，垃圾回收器就不会回收被引用的对象。
- 软引用：Soft Reference
  - 软引用是一种内存敏感的引用类型。垃圾回收器在内存不足时会回收这些对象。软引用通常用于实现内存敏感的缓存。
  - `SoftReference<String> softReference = new SoftReference<>(new String("Hello"));`
- 弱引用：
  - 弱引用比软引用更弱。垃圾回收器在下一次回收时，无论内存是否足够，**都会回收只被弱引用指向**的对象。弱引用适合用于实现没有阻止垃圾收集器回收对象的映射（例如，`WeakHashMap`）。
  - `WeakReference<String> weakReference = new WeakReference<>(new String("Hello"));`
- 虚引用：
  - 虚引用是最弱的一种引用类型。设置虚引用的唯一目的是在这个对象被垃圾回收器回收时收到一个系统通知。虚引用对于确定对象何时被从内存中移除非常有用，它们常被用于实现高级的内存管理和监控。
  - `PhantomReference<String> phantomReference = new PhantomReference<>(new String("Hello"), new ReferenceQueue<>());`

### 注解

#### 定义注解

- 注解是一种结构化接受检查的向代码中添加元数据的方法，可以帮助面出部署描述文件或其它生成文件的编写工作。是对代码的**一种特殊标记**，这些标记可以在编译，类加载和运行时被读取，并执行相应的处理

- 注解通常包含一些可以设定值的元素，程序或工具在处理注解时可以使用参数，没有任何元素的注解为标记注解


``` java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface name{
    int id()p;
    String description() default "no description";//默认值 
}

@UseCase(id = 47, description = "xxx")
public void...//绑定注解的方法
```

- 注解允许的元素类型：基本数据类型、String、Class、enum、Annotation（注解）、及以上类型的数组

- 元素要么有默认值，要么在使用时必须给出值

- 非基本类型不允许赋值为null

- 标准注解

  - @Override
  - @Deprecated：如果元素被使用了，则编译器会发出警告
  - @SuppressWarnings：关闭不当的编译警告
  - @SafeVarargs：在使用泛型作为可变参数的方法或构造器中关闭警告
  - @FunctionalInterface：声明函数式接口

- 元注解

  - 对注解进行注解
  - @Target：该注解可用的地方
    - ElementType.CONSTRUCTOR：构造器声明
    - ElementType.FIELD：字段声明（包括枚举常量）
    - ElementType.LOCAL_VARIABLE：本地变量声明
    - ElementType.METHOD：方法声明
    - ElementType.PACKAGE：包声明
    - ElementType.PARAMETRE：参数声明
    - ElementType.TYPE：类、接口（含注解）枚举的声明
  - @Retention：注解信息可以保存多久
    - RetentionPolicy.SOURCE：注解会被编译器丢弃
    - RetentionPolicy.CLASS：可以被编译器使用但是会被虚拟机丢弃
    - RetentionPolicy.RUNTIME：运行时被虚拟机保留，可以通过反射读取注解信息
  - @Documented：在javadoc中引入注解
  - @Inherited：允许子类继承父类注解
  - @Repeatable：可以多次应用于同一个声明

- 注解不支持继承，不能对@interface使用extends，但是可以通过内嵌注解实现类似的功能

- 嵌套注解

  - 嵌套注解通常用于当一个注解的**属性值**本身就是一个注解

  - 这就是一个嵌套注解


``` java
public @interface Uniqueness {
  Constraints constraints()
  default @Constraints(unique = true);
}
```

#### 注解处理器

- 例：读取被注解的类，通过反射查找注解标签，通过注解的id查找出已经注册的测试案例


``` java
public static void trackUseCases(List <Integer> useCases, Class <?> cl) {
    for(Method m : cl.getDeclaredMethods()) {
        UseCase uc = m.getAnnotation(UseCase.class);
        if(uc != null) {
            System.out.println("Found Use Case " +uc.id() + "\n  " + uc.description());
            useCases.remove(Integer.valueOf(uc.id()));
        }
    }
}
```

- 示例


``` java
@Target(ElementType.TYPE) // Applies to classes only
@Retention(RetentionPolicy.RUNTIME)
public @interface DBTable {
  String name() default "";
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Constraints {
  boolean primaryKey() default false;
  boolean allowNull() default true;
  boolean unique() default false;
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLInteger {
  String name() default "";
  Constraints constraints() default @Constraints;
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLString {
  int value() default 0;
  String name() default "";
  Constraints constraints() default @Constraints;
}

public @interface Uniqueness {
  Constraints constraints()
  default @Constraints(unique = true);
}

@DBTable(name = "MEMBER")
public class Member {
  @SQLString(30) String firstName;
  @SQLString(50) String lastName;
  @SQLInteger Integer age;
  @SQLString(value = 30,
  constraints = @Constraints(primaryKey = true))
  String reference;
  static int memberCount;
  public String getReference() { return reference; }
  public String getFirstName() { return firstName; }
  public String getLastName() { return lastName; }
  @Override public String toString() {
    return reference;
  }
  public Integer getAge() { return age; }
}
```

-  这套自定义注解的意义在于提供一种**声明式的方法**来描述一个Java类**如何映射**到数据库的表和字段。通过这些注解，你可以将类和字段的元数据（例如表名、字段名、字段类型、约束等）直接嵌入到Java代码中。这种方式的好处是代码更清晰、更直观，并且可以由框架或工具在运行时动态解析，从而自动生成SQL语句或执行其他数据库相关操作。

- 注解处理器：根据注解读取文件创建SQL数据库


``` java
public class TableCreator {
  public static void
  main(String [] args) throws Exception {
    if(args.length < 1) {
      System.out.println(
        "arguments: annotated classes");
      System.exit(0);
    }
      //参数标识已经标注好需要进行自动构建的类
    for(String className : args) {
      Class <?> cl = Class.forName(className);
        //获取注解
      DBTable dbTable = cl.getAnnotation(DBTable.class);
      if(dbTable == null) {
        System.out.println(
          "No DBTable annotations in class " +
          className);
        continue;
      }
      String tableName = dbTable.name();
      // If the name is empty, use the Class name:
      if(tableName.length() < 1)
        tableName = cl.getName().toUpperCase();
      List <String> columnDefs = new ArrayList <>();
       //检查所有字段
      for(Field field : cl.getDeclaredFields()) {
        String columnName = null;
          //获取字段上的注解
        Annotation [] anns =
          field.getDeclaredAnnotations();
        if(anns.length < 1)
          continue; // Not a db table column
          //根据类型自动执行 SQL 命令进行构建
        if(anns [0] instanceof SQLInteger) {
          SQLInteger sInt = (SQLInteger) anns [0];
          if(sInt.name().length() < 1)
            columnName = field.getName().toUpperCase();
          else
            columnName = sInt.name();
          columnDefs.add(columnName + " INT" +
            getConstraints(sInt.constraints()));
        }
        if(anns [0] instanceof SQLString) {
          SQLString sString = (SQLString) anns [0];
          // Use field name if name not specified.
          if(sString.name().length() < 1)
            columnName = field.getName().toUpperCase();
          else
            columnName = sString.name();
          columnDefs.add(columnName + " VARCHAR(" +
            sString.value() + ")" +
            getConstraints(sString.constraints()));
        }
        StringBuilder createCommand = new StringBuilder(
          "CREATE TABLE " + tableName + "(");
        for(String columnDef : columnDefs)
          createCommand.append(
            "\n    " + columnDef + ",");
        // Remove trailing comma
        String tableCreate = createCommand.substring(
          0, createCommand.length() - 1) + ");";
        System.out.println("Table Creation SQL for " +
          className + " is:\n" + tableCreate);
      }
    }
  }
  private static
  String getConstraints(Constraints con) {
    String constraints = "";
    if(! con.allowNull())
      constraints += " NOT NULL";
    if(con.primaryKey())
      constraints += " PRIMARY KEY";
    if(con.unique())
      constraints += " UNIQUE";
    return constraints;
  }

```

##### 用javac处理-编译时注解*

- 通过javac可以创建编译时注解处理器，将注解用于java源文件而不是编译之后的文件。
- 不能通过注解来修改源代码，唯一能影响结果的方法是创建新文件
  - 如果注解创建了新文件，新一轮处理中会检查该文件自身的注解。工具会一轮一轮的持续处理，直到没有新的源文件被创建
- 需要创建一个注解处理器并绑定到编译器
  - `@SupportedAnnotationTypes("com.class")`指定注解处理器支持的注解类型
    - 这意味着注解处理器会处理所有被 `com.class` 注解标记的元素。
    - 使用这个注解能够提高处理器的查找效率，并且使得处理器的意图更加明确。
  - `@SupportedSourceVersion(SourceVersion.RELEASE_8)`注解用于指定注解处理器支持的源代码版本。在这个例子中，它表明处理器支持Java 8的源代码版本。
    - 避免在与处理器不兼容的Java版本上运行时可能出现的问题。
    - 默认情况下，注解处理器被假定为支持最新的源码版本。

- `Element` 是用于表示和操作元素在**源代码中**的结构和信息的基础。
  - **PackageElement**：表示一个**包**。提供对包名、包内成员等的访问。
  - **TypeElement**：表示一个**类或接口**。提供对类名、父类、接口、方法和字段等的访问。
  - **ExecutableElement**：表示某些**可执行**的元素，如方法、构造函数或初始化器（静态或实例）。
  - **VariableElement**：表示一个字段、枚举常量、方法或构造函数的参数、局部变量或异常参数。
  - `getKind()`：返回此元素的类型（如类、方法、字段等）。
  - `getModifiers()`：返回此元素的修饰符（如 `public`、`private`、`static` 等）。
  - `getSimpleName()`：返回此元素的简单（不带包名的）名称。
  - `getEnclosingElement()`：返回包含此元素的最近的上层元素。
  - `getAnnotation(Class<A> annotationType)`：返回此元素上特定类型的注解。

- 示例


``` java
//注解定义
@Retention(RetentionPolicy.SOURCE)
@Target({ElementType.TYPE, ElementType.METHOD,
         ElementType.CONSTRUCTOR,
         ElementType.ANNOTATION_TYPE,
         ElementType.PACKAGE, ElementType.FIELD,
         ElementType.LOCAL_VARIABLE})
public @interface Simple {
    String value() default "-default-";
}
//使用注解
@Simple
public class SimpleTest {
  @Simple
  int i;
  @Simple
  public SimpleTest() {}
  @Simple
  public void foo() {
    System.out.println("SimpleTest.foo()");
  }
  @Simple
  public void bar(String s, int i, float f) {
    System.out.println("SimpleTest.bar()");
  }
  @Simple
  public static void main(String [] args) {
    @Simple
    SimpleTest st = new SimpleTest();
    st.foo();
  }
}
//处理器（仅仅打印注解的信息）
@SupportedAnnotationTypes(
  "annotations.simplest.Simple")
@SupportedSourceVersion(SourceVersion.RELEASE_8)
//继承标识是一个注解处理器
public class SimpleProcessor
extends AbstractProcessor {
  @Override public boolean process(
    Set <? extends TypeElement> annotations,
    RoundEnvironment env) {
      //打印所有待处理的注解类型
    for(TypeElement t : annotations)
      System.out.println(t);
    for(Element el : env.getElementsAnnotatedWith(Simple.class))
        //显示所有被注解标记的元素
      display(el);
    return false;
  }
  private void display(Element el) {
    System.out.println("==== " + el + " ====");
    System.out.println(el.getKind() +
      " : " + el.getModifiers() +
      " : " + el.getSimpleName() +
      " : " + el.asType());
      //不同类型不同显示
    if(el.getKind().equals(ElementKind.CLASS)) {
      TypeElement te = (TypeElement)el;
      System.out.println(te.getQualifiedName());
      System.out.println(te.getSuperclass());
      System.out.println(te.getEnclosedElements());
    }
    if(el.getKind().equals(ElementKind.METHOD)) {
      ExecutableElement ex = (ExecutableElement)el;
      System.out.print(ex.getReturnType() + " ");
      System.out.print(ex.getSimpleName() + "(");
      System.out.println(ex.getParameters() + ")");
    }
  }
}
```

- 正常编译是不会使用注解处理器，要加上参数`-processor com.注解类`

- 在编译时注解处理器中不能使用反射，因为操作的是源代码而不是编译后的类，使用**镜子**查看源代码中的方法字段类型等信息


``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
public @interface ExtractInterface {
    String interfaceName() default "-!!-";
}
//一个简单的乘法计算类
@ExtractInterface(interfaceName = "IMultiplier")
public class Multiplier {
    public boolean flag = false;
    private int n = 0;
    public int multiply(int x, int y) {
        int total = 0;
        for(int i = 0; i < x; i++)
            total = add(total, y);
        return total;
    }
    public int fortySeven() { return 47; }
    private int add(int x, int y) {
        return x + y;
    }
    public double timesTen(double arg) {
        return arg * 10;
    }
    public static void main(String [] args) {
        Multiplier m = new Multiplier();
        System.out.println(
            " 11 * 16 = " + m.multiply(11, 16));
    }
}
//处理器，提取出方法创建新接口的源代码文件
@SupportedAnnotationTypes(
    "annotations.ifx.ExtractInterface")
@SupportedSourceVersion(SourceVersion.RELEASE_8)
public class IfaceExtractorProcessor
    extends AbstractProcessor {
    //存储符合条件的方法字段
    private ArrayList <Element>
        interfaceMethods = new ArrayList <>();
    Elements elementUtils;
    private ProcessingEnvironment processingEnv;
    @Override public void init(ProcessingEnvironment processingEnv) {
        this.processingEnv = processingEnv;
        elementUtils = processingEnv.getElementUtils();
    }
    @Override public boolean process(
        Set <? extends TypeElement> annotations,
        RoundEnvironment env) {
        //处理有目标注解的字段
        for(Element elem: env.getElementsAnnotatedWith(
            ExtractInterface.class)) {
            //获取设置的名称参数
            String interfaceName = elem.getAnnotation(
                ExtractInterface.class).interfaceName();
            //检查目标类的所有封闭元素（字段、方法、构造器等）
            for(Element enclosed :
                elem.getEnclosedElements()) {
                
                //寻找 public static 方法
                if(enclosed.getKind()
                   .equals(ElementKind.METHOD) &&
                   enclosed.getModifiers()
                   .contains(Modifier.PUBLIC) &&
                   ! enclosed.getModifiers()
                   .contains(Modifier.STATIC)) {
                    interfaceMethods.add(enclosed);
                }
            }
            if(interfaceMethods.size() > 0)
                writeInterfaceFile(interfaceName);
        }
        return false;
    }
    //创建并写入接口
    private void
        writeInterfaceFile(String interfaceName) {
        try(
            Writer writer = processingEnv.getFiler()
            .createSourceFile(interfaceName)
            .openWriter()
        ) {
            String packageName = elementUtils
                .getPackageOf(interfaceMethods
                              .get(0)).toString();
            writer.write(
                "package " + packageName + ";\n");
            writer.write("public interface " +
                         interfaceName + " {\n");
            for(Element elem : interfaceMethods) {
                ExecutableElement method =
                    (ExecutableElement)elem;
                String signature = "  public ";
                signature += method.getReturnType() + " ";
                signature += method.getSimpleName();
                signature += createArgList(
                    method.getParameters());
                System.out.println(signature);
                writer.write(signature + ";\n");
            }
            writer.write("}");
        } catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
    private String createArgList(
        List <? extends VariableElement> parameters) {
        String args = parameters.stream()
            .map(p -> p.asType() + " " + p.getSimpleName())
            .collect(Collectors.joining(", "));
        return "(" + args + ")";
    }
}
```

- 得到的文件


``` java
//IMultiplier.java
package annotations.ifx;
public interface IMultiplier{
    public int multiply(int x, int y);
    public int fortySeven();
    public double timesTen(double arg);
}
```

### 函数式编程

#### Lambda表达式

- 简化函数式接口的使用


``` java
// 定义一个函数式接口
@FunctionalInterface
interface MyFunction {
    int apply(int a, int b);
}

public class LambdaExample {
    public static void main(String [] args) {
        // 使用 Lambda 表达式实现函数式接口
        MyFunction add = (a, b) -> a + b;
        MyFunction subtract = (a, b) -> a - b;

        // 调用函数对象
        int result1 = add.apply(5, 3);
        int result2 = subtract.apply(5, 3);

        System.out.println("Addition result: " + result1);
        System.out.println("Subtraction result: " + result2);
    }
}
```

- `()->{}`

  - 只有一个参数时可以省略()，没有参数时不可以省略
  - 只有一行代码时可以省略{}以及return

- 递归函数`fact = n->n==0?1:n*fact.call(n-1)`

  - 对于lambda变量使用call进行调用


#### 方法引用

- 类名或对象名+::+方法名

  - 自然，非静态方法使用对象名，静态方法使用类名


``` java
interface Callable {                        // [1]
  void call(String s);
}

class Describe {
  void show(String msg) {                   // [2]
    System.out.println(msg);
  }
}

public class MethodReferences {
  static void hello(String name) {          // [3]
    System.out.println("Hello, " + name);
  }
  static class Description {
    String about;
    Description(String desc) { about = desc; }
    void help(String msg) {                 // [4]
      System.out.println(about + " " + msg);
    }
  }
  static class Helper {
    static void assist(String msg) {        // [5]
      System.out.println(msg);
    }
  }
  public static void main(String [] args) {
    Describe d = new Describe();
    Callable c = d:: show;                   // [6]
    c.call("call()");                       // [7]

    c = MethodReferences:: hello;            // [8]
    c.call("Bob");

    c = new Description("valuable"):: help;  // [9]
    c.call("information");

    c = Helper:: assist;                     // [10]
    c.call("Help!");
  }
}
```

- 可以使用具有和接口相同签名（参数类型和返回值）的方法引用来实现接口。

- Thead对象创建要求传入实现Runnable接口的对象


``` java
class Go {
  static void go() {
    System.out.println("Go:: go()");
  }
}

public class RunnableMethodReference {
  public static void main(String [] args) {

    new Thread(new Runnable() {
      public void run() {
        System.out.println("Anonymous");
      }
    }).start();
	//lambda 和方法引用都不需要再定义 run 方法
    new Thread(
      () -> System.out.println("lambda")
    ).start();

    new Thread(Go:: go).start();
  }
}
```

- 未绑定的方法引用

  - 对于非静态方法不能直接通过类访问，因为这缺少缺少this参数


``` java
class X {
  String f() { return "X:: f()"; }
}

interface MakeString {
  String make();
}

interface TransformX {
  String transform(X x);
}

public class UnboundMethodReference {
  public static void main(String [] args) {
    // MakeString ms = X:: f;                // [1]
    TransformX sp = X:: f;
    X x = new X();
    System.out.println(sp.transform(x));    // [2]
    System.out.println(x.f()); // Same effect
  }
}
```

- 神奇的是如果接口参数多一个对象类型，就你不了缺少对向的问题

- 构造器方法引用

  - 使用参数匹配的接口捕获不同的构造方法


``` java
class Dog {
  String name;
  int age = -1; // For "unknown"
  Dog() { name = "stray"; }
  Dog(String nm) { name = nm; }
  Dog(String nm, int yrs) { name = nm; age = yrs; }
}

interface MakeNoArgs {
  Dog make();
}

interface Make1Arg {
  Dog make(String nm);
}

interface Make2Args {
  Dog make(String nm, int age);
}

public class CtorReference {
  public static void main(String [] args) {
      //名字都是 new
    MakeNoArgs mna = Dog:: new;        // [1]
    Make1Arg m1a = Dog:: new;          // [2]
    Make2Args m2a = Dog:: new;         // [3]

    Dog dn = mna.make();
    Dog d1 = m1a.make("Comet");
    Dog d2 = m2a.make("Ralph", 4);
  }
}
```

#### 函数式接口

函数式接口是只包含一个抽象方法声明的接口

- lambda表达式是函数式接口的实例，是与其关联的目标类型（即函数式接口描述lambda的类型，而lambda是特定类型函数式接口的一个具体实例）

  - 每个 Lambda 表达式都能隐式地赋值给函数式接口

- ```java
  Runnable r = () -> System.out.println("hello world");
  ```

- 函数式接口要求接口中**只能有一个**抽象方法

  - 可以使用`@FunctionalInterface`（是可选的，会检查是否满足只有一个抽象方法的条件）
  - 称作单一抽象方法类型

- 内置函数接口类型(java.util.function)

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231112235132480.png" alt="image-20231112235132480" style="zoom:33%;" />

    - 处理基本数据类型只是出于性能考虑，实际上会自动装箱拆箱


``` java
static Function <Foo,Bar> f1 = f -> new Bar(f);
static IntFunction <IBaz> f2 = i -> new IBaz(i);
static LongFunction <LBaz> f3 = l -> new LBaz(l);
static DoubleFunction <DBaz> f4 = d -> new DBaz(d);
static ToIntFunction <IBaz> f5 = ib -> ib.i;
static ToLongFunction <LBaz> f6 = lb -> lb.l;
static ToDoubleFunction <DBaz> f7 = db -> db.d;
static IntToLongFunction f8 = i -> i;
static IntToDoubleFunction f9 = i -> i;
static LongToIntFunction f10 = l -> (int)l;
static LongToDoubleFunction f11 = l -> l;
static DoubleToIntFunction f12 = d -> (int)d;
static DoubleToLongFunction f13 = d -> (long)d;

Bar b = f1.apply(new Foo());
IBaz ib = f2.apply(11);
LBaz lb = f3.apply(11);
DBaz db = f4.apply(11);
int i = f5.applyAsInt(ib);
long l = f6.applyAsLong(lb);
double d = f7.applyAsDouble(db);
l = f8.applyAsLong(12);
d = f9.applyAsDouble(12);
i = f10.applyAsInt(12);
d = f11.applyAsDouble(12);
i = f12.applyAsInt(13.0);
l = f13.applyAsLong(13.0);
```

- 使用apply、get、compare等方法调用（见图片）

- 方法引用也可以使用函数是接口

#### 高阶函数

- 把函数作为参数或返回值的函数

- 需要配合函数式接口使用

- 把函数作为返回值


``` java
//可以对内置的函数式接口进行重命名（通过继承）
interface
FuncSS extends Function <String, String> {}
public class ProduceFunction {
  static FuncSS produce() {
    return s -> s.toLowerCase();//函数作为返回值
  }
  public static void main(String [] args) {
    FuncSS f = produce();
    System.out.println(f.apply("YELLING"));
  }
}
```

- 函数作为参数时同样使用函数式接口限制

#### 闭包

- 如果一个lambda使用了函数作用域之外的变量，解决这个问题就是支持闭包
- 如果一个lambda表达式中包含了局部变量（比如一个返回值为函数的函数中的一个局部变量），这这个变量必须为final（至少不会被修改（即实际上的final变量））
  - 也可以将局部变量赋值给final变量再交给lambda表达式处理
  - 引用类型的是可以修改指向的对象的（设计final原理），但是包装类型不可以
  - 将内部类作为返回值时也需要注意类似的问题

#### 杂项

- 函数组合

  - 将多个函数结合使用


``` java
	xxxxxxxxxx4 1return in.andThen(o -> {2    System.out.println(o);3    return o;4});
```

- `f.andThen()`在f之后调用

- `.compose()`在f之前调用

- 会得到一个新函数

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231113004543975.png" alt="image-20231113004543975" style="zoom:50%;" />


``` java
//函数组合
f4 = fa.compose(f2).andThen(f3);
//逻辑
public class PredicateComposition {
  static Predicate <String>
    p1 = s -> s.contains("bar"),
    p2 = s -> s.length() < 5,
    p3 = s -> s.contains("foo"),
    p4 = p1.negate().and(p2).or(p3);
  public static void main(String [] args) {
    Stream.of("bar", "foobar", "foobaz", "fongopuckey")
      .filter(p4)
      .forEach(System.out:: println);
  }
}
//不包含并且 或者...
```

- 柯里化和部分求值

  - 将一个接受多个参数的函数转变为一系列只接受一个参数的函数
  - 每接收一个新参数就得到一个新函数，直至得到结果


``` java
import java.util.function.*;

public class Curry3Args {
   public static void main(String [] args) {
      Function < String,
        Function < String,
          Function <String, String> >> sum =
            a -> b -> c -> a + b + c;
      Function < String,
        Function <String, String> > hi =
          sum.apply("Hi ");
      Function <String, String> ho =
        hi.apply("Ho ");
       sum.apply("Hup ").apply("Ho ").apply("Hup");
      System.out.println(ho.apply("Hup"));
   }
}
```

### 流

- 使用流，只需要说明想做什么，类似声明式编程，说明要做什么而不说明如何完成

  - ``` java
    //选择再 5-20 之间从小到大不同的 7 个随机数
    new Random(47)
          .ints(5, 20)
          .distinct()
          .limit(7)
          .sorted()
          .forEach(System.out:: println);
    ```

- 流的列表是一个“延迟列表”（延迟求值），可以表示非常大的序列而不需要考虑内存的问题

- `import java.util.stream.*`

- 流类型`Stream<>`

#### 创建流

- 从一组条目创建`Stream.of(a,b,c...).`

- 从Collection创建`Collection.stream().`

  - Map要先`.entrySet()`生成每个对象包含一个键与其关联的值

- 随机数流


``` java
Random rand = new Random(47);
rand.ints()//无参数无限流
rand.ints(n)//一个参数表示流的大小
rand.ints(down, up)//两个参数表示上下边界
rand.ints(n, down, up )
```

- 只可以生成int、long、double

- 使用[.boxed()]打包为包装类型

- `range(from, to)`

  - 生成int流[from, to)可以用于循环
  - `for(int i:range(10,20).toArray())`

- `generate()`

  - 用于产生由完全相同的对象组成的流
  - 需要将一个生成对象的lambda表达式传入
    - `Stream.generate(()->"duplicate")...`
    - 创建一个由重复字符串组成的流
  - 也可以传入一个方法引用

- `iterate()`

  - 从一个参数开始，将其传递给第二个参数引用的方法，结果添加到流并作为下一次调用的第一个参数

  - 生成斐波那契序列


``` java
public class Fibonacci {
  int x = 1;
  Stream <Integer> numbers() {
    return Stream.iterate(0, i -> {
      int result = x + i;
      x = i;
      return result;
    });
  }
}
```

- 流生成器

  - 创建一个生成器对象用于生成
  - 创建`Stream.Builder<String> builder = Stream.builder();`
  - 使用`.add()`向生成起添加元素
  - 构建`builder.build()`构建之前可以随意添加元素
    - 直接使用build后的结果就可以获取流	

- Arrays

  - 将数组转化为流`Arrays.stream(arrp[],[from, end])`
  - 可以只是选择的范围
  - 对于基本类型会产生IntStream、LongStream、DoubleStream

- 使用正则表达式切割字符串得到字符串流
  - `import java.util.regex.Pattern`
  - `Pattern.compile("正则").splitAsStream(String);`
- 创建空流
  - `Stream<String? s = Stream.empty();`

- 使用迭代器从流获取元素


``` java
Stream <Integer> stream = new Random().ints(0, 100).boxed().distinct();
        Iterator <Integer> iterator = stream.iterator();
```

- 通过操作迭代器进行访问


#### 修改流元素

- `skip(n)`丢弃前n个元素

- 查看流对象`peek(System.out::print)`

  - 要求输入Consumer函数式接口，即没有返回值，也就不会对流修改

- `sorted([比较函数])`

  - 内置比较如`Comparator.reverseOrder()`

  - 使用lambda（创建匿名类）


``` java
List <Person> sortedByAge = people.stream()
    .sorted((p1, p2) -> Integer.compare(p1.getAge(), p2.getAge()))
    .collect(Collectors.toList());
```

- 移除重复元素`distinct()`

- `filter()`过滤元素

  - 过滤操作只保留传给过滤函数后返回值为true的元素


``` java
public static boolean isPrime(long n){
    return rangeClosed(2,(long)Math.sqrt(n))
        .noneMath(i-> n%i==0);//有一项不满足就终止并返回 false
}
//生成素数
public LongStream numbers(){
    return iterate(2, i-> i+1)
        .filter(Prime:: isPrime);
}
```

- `map(Function)`

  - 输入流中的对象，结果作为输出流继续传递
  - `mapToInt(TOintFunction)`等基本类型map

- 组合流

  - 如果在map函数中生成的是流，那我们得到了一个有流组成的嵌套流
    - `flatMap(Function)`接受生成流的函数，并对生成的流进行扁平化展开为元素
    - `flatMapToInt(Function)`用于处理基础类型
  - `concat()`用于组合两个流

- 从文件创建

  - 按照单词创建流


``` java
public class FileToWords {
  public static Stream <String> stream(String filePath)
  throws Exception {
    return Files.lines(Paths.get(filePath))
      .skip(1) // First (comment) line
      .flatMap(line -> Pattern.compile("\\W+").splitAsStream(line));
      //	将对行拆分的字符串流扁平化
  }
}
```

#### Optional类型

- `Optional`是一个容器对象，它可能包含一个值，也可能不包含。使用`Optional`可以更优雅地处理可能为空的情况

- 不只可以用于流，也可以在任何涉及null的地方使用，以减少异常，方便对null进行处理

- **某些标准流操作会返回Optional对象**

  - 流操作经常以`Optional`结束

- `findFirst()`返回包含第一个元素的Optional，如果流为空则返回`Optional.empty`

``` java
//逐个获取
for...
stream
    .skip(i)
    .findFirst()
```

- `findAny()`返回包含任何元素的Optional

- `max() min()`

- 数值化流（IntStream）的`average()`

- 流聚合`reduce([T identity可选初始值], BinaryOperator<T> accumulator);`

  - 对所有元素进行操作合并，得到聚合后的汇总结果
  - `reduce(0, (a, b) -> a + b);`获取元素的总和

- 便捷函数：对Optional中的数据进行快捷操作（定义在null时进行的操作）

  - `ifPresent(Consumer)`如果**值存在**就用值来**调用**Consumer（一个参数没有返回值）

  - `orElse(otherObject)`如果对象存在则返回对象否则返回otherObject

  - `orElseGet(Supplier)`不存在则执行一个 `Supplier` 函数并返回其结果。

  - `orElseThrow(Supplier)`不存在则抛出由函数创建的异常


``` java
//为 null 则怕抛出异常 
title = Optional.ofNullable(newTitle)
      .orElseThrow(EmptyTitleException:: new);
//为 null 则自动创建新对象
person = Optional.ofNullable(newPerson)
      .orElse(new Person());
```

- 创建Optional

  - 静态方法
  - `Optional.empty()`返回空Optional
  - `of(value)`要求value不是null
  - `ofNullable(vlaue)`允许为null，会自动返回`Optional.empty`

- Optional上的操作
  - 修改
    - `filter(Predicate)`将函数用于Optional的内容并返回结果（返回false则转化为empty），如果Optional和函数不匹配就转化为empty（而不是直接删除）（本身就是empty则直接返回）
    - `map(Function)`应用Function对非empty元素进行转化
    - `flatMap(Function)`由提供的Function把结果包装在Optional中，flatMap不会再做任何包装（需要在Function中创建Optional）

- Optional流

  - 创建`stream.map(signal -> Optional.ofNullable(signal))`

  - 获得Optional中的对象


``` java
stream
    .filter(Optional:: isPresent)//保留非 empty
    .map(Optional:: get)获取对象
    ...
```

#### 消费流元素

- 接受一个流并生成最终结果，通常作为最后一件事

- 将流转化为数组

  - `toArray()`自动转换到恰当类型的数组

- 对每个流元素进行操作

  - `forEach(Consumer)`如输入`System.out::println`
    - 使用多处理器`parallel()`时顺序是不确定的
  - `forEachOrdered(Consumer)`确保按照原始流顺序进行

- 收集

  - `collect(Collector)`将元素累加到一个结果集合

  - 指定生成的集合类型`collect(Collectors.toCollection(TreeSet::new))`


``` java
public class TreeSetOfWords {
  public static void
  main(String [] args) throws Exception {
    Set <String> words2 =
      Files.lines(Paths.get("TreeSetOfWords.java"))
        //根据非单词字符划分
        .flatMap(s -> Arrays.stream(s.split("\\W+")))
        //去除非数字
        .filter(s -> ! s.matches("\\d+")) // No numbers
        //去除左右空白
        .map(String:: trim)
        //去除太短
        .filter(s -> s.length() > 2)
        .limit(100)
//指定生成的集合类型        
.collect(Collectors.toCollection(TreeSet:: new));
    System.out.println(words2);
  }
}
```

- 转化为map`Collectors.toMap(获取key的函数,获取val的函数)`

- 大部分时候都可以直接使用预定义的Collector

- `collect(Supplier, BiConsumer, BiConsumer)`创建新集合,将下一个元素包含到结果中,将两个值组合起来

  - `collector(ArrayList::new,ArrayList::add,ArrayList::addAll)`

- 组合

  - `reduce([identity(组合的初始值),] BinaryOperator)`组合所有流元素,返回Optional

- 匹配

  - `allMatch(Predicate)`每一个元素都是true才返回true
  - `anyMatch(Predicate)`任何一个为true就返回true
  - `noneMatch(Predicate)`没有true
  - 都是短路计算

- 选择

  - `findFirst()`
  - `findAny()`对于非并行的流也是返回第一个
  - 获取最后一个元素`reduce((n1,n2)->n2)`

- 获取信息

  - `count()`元素数目
  - `max(Comparator)`
    - 许多类型有预定义的比较器,如`String.CASE_INSENSITIVE_ORDER`
    - 数值化流不需要比较器
  - `min(Comparator)`
  - 数值化流
    - `average()`
    - `sum()`
    - `summaryStatistics()`获取摘要数据(用于查看数值流的状态)

### 包

- 包本质来说就是**文件夹**, 用来管理类文件的
- **域名路径要匹配文件夹路径**
- 建包语句必须在第一行`package com.itheima.domain;`类似倒写域名
  - 必须是文件中第一行非注释代码
  - 包名只包含文件路径（即不包含最后一级具体的java文件），因此一个包（文件夹）中可以有多个java文件
- 使用包外的类要用完整的名称`new hiding.mypackage.MyClass();`(或者使用import)
- 相同包下的类可以直接访问，不同包下的类必须导包,才可以使用！导包格式：`import 包名.类名;`
  - 假如一个类中需要用到不同类，而这个两个类的名称是一样的，那么默认只能导入一个类，另一个类要**带包名访问**。
  - 全部导入`import 包名.*`
- import后可能发生重名冲突，则要使用完整的路径访问类
- 静态引如`import static`
  - 允许在不指定类名的情况下**直接访问静态成员**
  - 导入类的所有静态成员`import static packageName.ClassName.*;`
- 对于没有设置包名的（路径）
  - 会被认为是默认包的隐藏部分，同属一个包，互相之间可以进行包内访问

### 数据压缩*

### 观察者

#### Observer（已弃用）

- Observer用于实现观察者模式

- 实现将调用的来源和被调用的代码以完全动态的方式解耦

- `update(Observable o, Object arg)`被观察者在变更时负责调用，第一个参数为被观察者，第二个参数为可能需要的额外信息

- 一个Observer可以注册多个Observable

- Observable有`notifyObservers()`方法，用于调用相应注册的Observer的`update()`方法

  - 还有一个标志位changed，只有changed被设置时`notifyObservers()`才有效（先清除changed后通知），否则不起作用
  - 使用`setChanged()`设置


``` java
//被观察者
class MyObservable extends Observable {
    private String data;

    public void setData(String data) {
        this.data = data;
        setChanged();  // 标记状态已改变
        notifyObservers(data);  // 通知观察者
    }
}
//观察者
class MyObserver implements Observer {
    @Override
    public void update(Observable o, Object arg) {
        System.out.println("Received response: " + arg);
    }
}

//注册及测试
MyObservable observable = new MyObservable();
MyObserver observer = new MyObserver();
observable.addObserver(observer);
observable.setData("New Data");

```

- 线程不安全

### 编码规范

- 规范原则
  - 尽量使用完整的英文描述符；
  - 采用大小写混合使名字可读，采用适用于相关领域的术语；
  - 尽量少用缩写，若已使用尽量明智，且在整个文件或工程中通用；
  - 避免使用长的和类似的名字，或仅仅是大小写不同的名字；
  - 除静态常量外，尽量少用下划线。
- 源文件命名规则
  - 源程序中包含有公共类的定义，源文件名必须**与该公共类的名字一致**。在一个源程序中**至多只能有一个公共类的定义**；
  - 源程序中不包含公共类，则该文件名只要和某个类名字相同即可；
  - 源程序中有多个类的定义，编译时将会为每个类生成一个class文件
- 包：包名是全小写的名词，中间可以由点分隔开，如java.awt.event
- **类／接口**：类／接口名**首字母大写**，若由多个单词合成一个类名，要求**每个单词的字母也要大写**，如MyFirstJava
- **方法**：由多个单词组成的方法名**首字母小写**，中间的每个单词首字母大写，如isButtonPressed
- 变量： 一般**全小写**，如length
- 常量：一般**全大写**，如果由多个单词组成则中间用下划线相连。如果是对象类型的常量，则是大小写混合，由大写字母把单词隔开，如STR_LENGTH
- 组件：使用完整的英文描述来说明组件的用途，尾部应该加上组件类型，如okButton

### 补充

- 随机数生成`import java.util.Random;`

  - `Random r = new Random();`
  - `int num = r.nextInt(20);`（0-19）

- 命令行参数的使用


``` shell
$ javac CommandLine.java 
$ java CommandLine this is a command line 200 -100
args [0]: this
args [1]: is
args [2]: a
args [3]: command
args [4]: line
args [5]: 200
args [6]: -100
```

## 环境配置

- `java-version`正常但是找不到`javac`：
  - 新建->变量名“CLASSPATH”,变量值`“.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar”`

## Javadoc

- 生成`javadoc -d doc -sourcepath src -subpackages com.mycompany`

  - 生成源代码路径 `src` 下 `com.mycompany` 包（及其子包）的Javadoc，并将文档输出到 `doc` 目录。

- 只会为public和protected成员处理文档，生成时添加`-private`将private纳入考虑

- 分类

  - 类的注释
  - 字段注释
  - 方法注释


``` java
import java.util.*;

/** The first On Java 8 example program.
 * Displays a String and today's date.
 * @author Bruce Eckel
 * @author www.MindviewInc.com
 * @version 5.0
 */
public class HelloDateDoc {
  /** Entry point to class & application.
   * @param args array of String arguments
   * @throws exceptions No exceptions thrown
   */
  public static void main(String [] args) {
    System.out.println("Hello, it's: ");
    System.out.println(new Date());
  }
}
```

### 基本语法

- 所有Javadoc指令都在`/**  */`格式的注释内

#### 嵌入式HTML

``` java
/** You can <em> even </em> insert a list:
 * <ol>
 * <li> Item one
 * <li> Item two
 * <li> Item three
 * </ol>
 */
public class Documentation3 {}
```

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231121151640157.png" alt="image-20231121151640157" style="zoom:33%;" />

- 行首的星号和空格会抛弃

- 不要使用标题标签

#### 标签

- 独立doc标签以@开头必须在每行注释的开头
- 行内标签同样以@开头但是可以位于注释的任意位置，并且需要用{}包住

| 标签          | 描述                                                   | 示例                                                         |
| ------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| @author       | 标识一个类的作者，一般用于类注释                       | @author description                                          |
| @deprecated   | 指名一个过期的类或成员，表明该类或方法不建议使用       | @deprecated description                                      |
| {@docRoot}    | 指明当前文档根目录的路径                               | Directory Path                                               |
| @exception    | 可能抛出异常的说明，一般用于方法注释                   | @exception exception-name explanation                        |
| {@inheritDoc} | 从直接父类继承的注释                                   | Inherits a comment from the immediate surperclass.           |
| {@link}       | 插入一个到另一个主题的链接                             | {@link name text}                                            |
| {@linkplain}  | 插入一个到另一个主题的链接，但是该链接显示纯文本字体   | Inserts an in-line link to another topic.                    |
| @param        | 说明一个方法的参数，一般用于方法注释                   | @param parameter-name explanation                            |
| @return       | 说明返回值类型，一般用于方法注释，不能出现再构造方法中 | @return explanation                                          |
| @see          | 指定一个到另一个主题的链接                             | @see anchor <br>@see classname#methodname                    |
| @serial       | 说明一个序列化属性                                     | @serial description                                          |
| @serialData   | 说明通过 writeObject() 和 writeExternal() 方法写的数据 | @serialData description                                      |
| @serialField  | 说明一个 ObjectStreamField 组件                        | @serialField name type description                           |
| @since        | 说明从哪个版本起开始有了这个函数                       | @since release                                               |
| @throws       | 和 @exception 标签一样.                                | The @throws tag has the same meaning as the @exception tag.  |
| {@value}      | 显示常量的值，该常量必须是 static 属性。               | Displays the value of a constant, which must be a static field. |
| @version      | 指定类的版本，一般用于类注释                           | @version info                                                |