
## 数据类型

### [[基本数据类型与包装类]]

### [[数组类型]]

### 枚举类型的定义

``` java
enum GENDER {
    MALE, FEMALE, NOTTELLING
}

public static void main(String [] args){
    GENDER gender = GENDER.MALE; // 每个枚举值都是是枚举类型的对象示例
    System.out.println(gender.toString() + " " + gender.ordinal()) //编译器自动生成
}
```

- 每个枚举类型对象都有`toString()`方法，用于打印实例名称（或使用name()）

- 使用`类名.values()`获取该枚举类所有对象的数组

- 枚举类型都隐式地继承自`java.lang.Enum`类，因此枚举类不能再去继承其他类，但是可以去实现接口
  - 可以直接向上转型为Enum
  - Enum向下转型`e.getClass().getEnumContants()`没有枚举的类型调用会返回null

- 枚举类可以添加自定义方法，枚举类也可以持有数据（并且定义构造函数）


``` java
public enum OzWitch {
  WEST("Miss"),
  NORTH("Glinda"),
  EAST("Wicked"),
  SOUTH("Good");
  private String description;
  private OzWitch(String description) {
    this.description = description;
  }
  public String getDescription() { return description; }
}
```

- 要注意的是这个构造函数是private的，仅限于内部初始化预定的枚举实例，不能在外部使用new创建任意对象


```java
public enum ConstantSpecificMethod {
  DATE_TIME {
    @Override String getInfo() {
      return
        DateFormat.getDateInstance()
          .format(new Date());
    }
  },
  CLASSPATH {
    @Override String getInfo() {
      return System.getenv("CLASSPATH");
    }
  },
  VERSION {
    @Override String getInfo() {
      return System.getProperty("java.version");
    }
  };
  abstract String getInfo();
  public static void main(String [] args) {
    for(ConstantSpecificMethod csm : values())
      System.out.println(csm.getInfo());
  }
}
```

- 分组枚举
  - 使用接口实现，外层接口没有定义任何方法，因此任何一个类都可以实现这个接口，每个枚举类都实现这个接口是为了按照一个类型来使用

``` java
public interface Food {
  enum Appetizer implements Food {
    SALAD, SOUP, SPRING_ROLLS;
  }
  enum MainCourse implements Food {
    LASAGNE, BURRITO, PAD_THAI,
    LENTILS, HUMMUS, VINDALOO;
  }
  enum Dessert implements Food {
    TIRAMISU, GELATO, BLACK_FOREST_CAKE,
    FRUIT, CREME_CARAMEL;
  }
  enum Coffee implements Food {
    BLACK_COFFEE, DECAF_COFFEE, ESPRESSO,
    LATTE, CAPPUCCINO, TEA, HERB_TEA;
  }
}

package enums.menu;
import static enums.menu.Food.*;

public class TypeOfFood {
  public static void main(String [] args) {
    Food food = Appetizer.SALAD;
    food = MainCourse.LASAGNE;
    food = Dessert.GELATO;
    food = Coffee.CAPPUCCINO;
  }
}
```

- 嵌套的枚举


``` java
public enum Course {
    APPETIZER(Food.Appetizer.class),
    MAINCOURSE(Food.MainCourse.class),
    DESSERT(Food.Dessert.class),
    COFFEE(Food.Coffee.class);
    private Food [] values;
    private Course(Class <? extends Food> kind) {
        values = kind.getEnumConstants();
    }
    public Food randomSelection() {
        return Enums.random(values);
    }
}
```

### 枚举类型的集合
- EnumSet
  - 用于使用枚举类型标识状态，需要从枚举类型创建


``` java
//创建空枚举
EnumSet <AlarmPoints> points =
      EnumSet.noneOf(AlarmPoints.class);
points.add(BATHROOM);
//从另一个 EnumSet 加入
points.addAll(EnumSet.of(STAIR1, STAIR2, KITCHEN));
//从 Enum 加入
points = EnumSet.allOf(AlarmPoints.class);
//从 EnumSet 删除
points.removeAll(EnumSet.of(STAIR1, STAIR2, KITCHEN));
points.removeAll(EnumSet.range(OFFICE1, OFFICE4));
points = EnumSet.complementOf(points);
```

- 通过long存储标志实现，支持多余64个枚举类型（会自动给引入新的long）
- 存储顺序由在Enum中的声明顺序决定

- EnumMap
  - 所有的键来源某个枚举类型（实际上就是一个数组）除此之外与普通map一样
  - **所有的枚举类型**会被作为键初始化加入到map，但是对应的val为**null**

#### 枚举类型的高级应用

- 职责链
  - 把解决问题的方法串成链，当请求到达时会顺着链传递下去知道遇到某个可以处理请求的方法（事件冒泡）


``` java
//一个邮局，针对不同类型的邮件有一系列处理方式，根据优先级高低一次对邮件进行处理直至成功
public class PostOffice {
  enum MailHandler {
    GENERAL_DELIVERY {
      @Override boolean handle(Mail m) {
        switch(m.generalDelivery) {
          case YES:
            System.out.println(
              "Using general delivery for " + m);
            return true;
          default: return false;
        }
      }
    },
    MACHINE_SCAN {
      @Override boolean handle(Mail m) {
        switch(m.scannability) {
          case UNSCANNABLE: return false;
          default:
            switch(m.address) {
              case INCORRECT: return false;
              default:
                System.out.println(
                  "Delivering "+ m + " automatically");
                return true;
            }
        }
      }
    },...
    };
    //每个手段的处理方式
    abstract boolean handle(Mail m);
  }
  static void handle(Mail m) {
      //按照枚举类型的声明顺序依次尝试处理
    for(MailHandler handler : MailHandler.values())
      if(handler.handle(m))
        return;
    System.out.println(m + " is a dead letter");
  }
}
```

- 有限状态机
  - 通常使用switch处理输入，选择下一步的状态进行状态转移


``` java
public class StateMachine {
    private State currentState;

    public StateMachine() {
        currentState = State.START;
    }

    // 根据当前状态和给定的输入决定下一个状态
    public void nextState(String input) {
        switch (currentState) {
            case START:
                if (input.equals("start")) {
                    currentState = State.IN_PROGRESS;
                } else {
                    currentState = State.ERROR;
                }
                break;
            case IN_PROGRESS:
                if (input.equals("pause")) {
                    currentState = State.PAUSED;
                } else if (input.equals("finish")) {
                    currentState = State.FINISHED;
                } else {
                    currentState = State.ERROR;
                }
                break;
            case PAUSED:
                if (input.equals("resume")) {
                    currentState = State.IN_PROGRESS;
                } else if (input.equals("stop")) {
                    currentState = State.FINISHED;
                } else {
                    currentState = State.ERROR;
                }
                break;
            case FINISHED:
                // 这里可能会有一些重置逻辑
                break;
            case ERROR:
                // 处理错误
                break;
        }
    }
    public State getCurrentState() {
        return currentState;
    }
}

```

- 多路分发

  - 方法的调用依赖于**多个对象的动态类型**（动态绑定是单路分发）

  - 有几路就进行几次方法调用，每次确定一个类型

  - 可以列举所有的组合，如两个参数都从3个固定类型选择，只需有枚举所有情况，即给每个类型都设置全部3种参数的成员函数

- 枚举类型分发

``` java
public enum RoShamBo2 implements Competitor <RoShamBo2> {
  PAPER(DRAW, LOSE, WIN),
  SCISSORS(WIN, DRAW, LOSE),
  ROCK(LOSE, WIN, DRAW);
  private Outcome vPAPER, vSCISSORS, vROCK;
  RoShamBo2(Outcome paper,
    Outcome scissors, Outcome rock) {
    this.vPAPER = paper;
    this.vSCISSORS = scissors;
    this.vROCK = rock;
  }
  @Override public Outcome compete(RoShamBo2 it) {
    switch(it) {
      default:
      case PAPER: return vPAPER;
      case SCISSORS: return vSCISSORS;
      case ROCK: return vROCK;
    }
  }
  public static void main(String [] args) {
    RoShamBo.play(RoShamBo2.class, 20);
  }
}
```

- 常量特定方法


``` java
//即枚举所有可能的组合 
PAPER {
    @Override public Outcome compete(RoShamBo3 it) {
      switch(it) {
        default: // To placate the compiler
        case PAPER: return DRAW;
        case SCISSORS: return LOSE;
        case ROCK: return WIN;
      }
    }
  }...
```

- EnumMap

- 嵌套实现

``` java
enum RoShamBo5 implements Competitor <RoShamBo5> {
  PAPER, SCISSORS, ROCK;
  static EnumMap <RoShamBo5,EnumMap<RoShamBo5,Outcome> >
    table = new EnumMap <>(RoShamBo5.class);
  static {
    for(RoShamBo5 it : RoShamBo5.values())
      table.put(it, new EnumMap <>(RoShamBo5.class));
    initRow(PAPER, DRAW, LOSE, WIN);
    initRow(SCISSORS, WIN, DRAW, LOSE);
    initRow(ROCK, LOSE, WIN, DRAW);
  }
  static void initRow(RoShamBo5 it,
    Outcome vPAPER, Outcome vSCISSORS, Outcome vROCK) {
    EnumMap <RoShamBo5,Outcome> row =
      RoShamBo5.table.get(it);
    row.put(RoShamBo5.PAPER, vPAPER);
    row.put(RoShamBo5.SCISSORS, vSCISSORS);
    row.put(RoShamBo5.ROCK, vROCK);
  }
  @Override public Outcome compete(RoShamBo5 it) {
      //获取方法
    return table.get(this).get(it);
  }
  public static void main(String [] args) {
    RoShamBo.play(RoShamBo5.class, 20);
  }
}
```

- 二维数组

  - 由于枚举类型可以转化为整数值，因此实际上可以直接使用二维数组


``` java
enum RoShamBo6 implements Competitor <RoShamBo6> {
  PAPER, SCISSORS, ROCK;
  private static Outcome [][] table = {
    { DRAW, LOSE, WIN }, // PAPER
    { WIN, DRAW, LOSE }, // SCISSORS
    { LOSE, WIN, DRAW }, // ROCK
  };
  @Override public Outcome compete(RoShamBo6 other) {
    return table [this.ordinal()][other.ordinal()];
  }
  public static void main(String [] args) {
    RoShamBo.play(RoShamBo6.class, 20);
  }
}
```

- 模式匹配的准备

  - switch箭头语法：(JDK14)

    - ``` java
      //传统方式
      case 1: xxx;
          break;
      //箭头语法
      case 1-> xxx;
      ```

    - 不能再一个switch种混用

  - cases null检查（JDK17）

    - 可以在switch内部检查是否为null
    - default并不会处理null的情况，会抛出Null异常
    - 这不是强制要求的

  - switch作为表达式（JDK14）

    - 使用`yield`返回结果，不需要（也不能）使用break；

    - 使用箭头表达式并且只有一个语句时可以省略yield

    - 不止有一个语句时用花括号

    - ``` java
      static int colon(String s) {
          var result = switch(s) {
            case "i": yield 1;
            case "j": yield 2;
            case "k": yield 3;
            default:  yield 0;
          };
          return result;
        }
        static int arrow(String s) {
          var result = switch(s) {
            case "i" -> 1;
            case "j" -> 2;
            case "k" -> 3;
            default  -> 0;
          };
          return result;
        }
      ```

  - 智能转型（JDK16）

    - ``` java
      static void smart(Object x) {
          if(x instanceof String s && s.length() > 0) {
            System.out.format(
              "%d %s%n", s.length(), s.toUpperCase());
          }
        }
      //特殊例子
      static void f(Object o) {
          if(!(o instanceof String s)) {
            System.out.println("Not a String");
            throw new RuntimeException();
          }
          //能到达这里说明一定是 String 类型，否则就出错了
          // s is in scope here!
          System.out.println(s.toUpperCase());  // [1]
        }
      ```

    - 使用`instanceof`判断类型后在接下来的作用域内就当作这个类型使用

    - 即已经判断成功了，那么这个作用域内当作这个类型处理显然是安全的

- 模式匹配（JDK17）

  - 允许根据数据的结构和内容来检查和分解数据。

  - 对于一组具有相同基类的类型，不止希望使用基类中的公共方法

  - 使用switch实现

    - ``` java
      public class PetPatternMatch {
          //使用非基类方法
        static void careFor(Pet p) {
          switch(p) {
            case Dog d -> d.walk();//使用了只能转型
            case Fish f -> f.changeWater();
            case Pet sp -> sp.feed();
          };
        }
        static void petCare() {
          List.of(new Dog(), new Fish())
            .forEach(p -> careFor(p));
        }
      }
      ```

  - 守卫：进一步细化匹配条件而不只是简单地匹配类型

    - 可以是任何布尔表达式，如果类型符合并且守卫为true那么就匹配上了

    - ``` java
      System.out.println(switch(s) {
          case Circle c && c.area() < 100.0
              -> "Small Circle: " + c;
          case Circle c -> "Large Circle: " + c;
          case Rectangle r && r.side1() == r.side2()
              -> "Square: " + r;
          case Rectangle r -> "Rectangle: " + r;
      });
      ```

  - 上面的case对下面的具有支配性

### 字符串

- String对象不可修改，每次修改实际上会创建一个新的对象
- StringBuilder
  - 允许在原有对象上修改字符串，避免反复创建新对象，在需要频繁修改的场景下效率更高
  - 常用方法
    - `append(...)`：向 `StringBuilder` 添加文本。
    - `insert(int offset, ...)`：在指定位置插入文本。
    - `delete(int start, int end)`：删除指定范围的字符。
    - `deleteCharAt(int index)`：删除指定位置的字符。
    - `replace(int start, int end, String str)`：替换指定范围的字符。
    - `toString()`：返回 `StringBuilder` 内容的字符串表示。

  - 最终转化为字符串`toString()`
  - 对于不涉及循环的简单字符串拼接`'a'+'b'+'c'`编译器会自动优化为StringBuilder


- 方法

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231117130837016.png" alt="image-20231117130836208" style="zoom:50%;" />

  - ![image-20231117130901638](https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231117130901638.png)

- 文本块（JDK15）

  - ``` java
    "" "
    xxx,
    xxx
    "" "
    ```

  - 不保留缩进

  - 自动换行

#### 格式化输出

- 使用printf/format（完全等价）

  - `system.out.printf("rOW 1: [%d %f]%n",x,y);`

- 生成新的字符串F

  - ``` java
    Formatter formatter = new Formatter();
    /*可以设置输出流
    StringBuilder sb = new StringBuilder();
    Formatter formatter = new Formatter(sb);
    */
    formatter.format("Name: %s, Age: %d", "Alice", 24);
    String result = formatter.toString(); // 结果为 "Name: Alice, Age: 24"
    ```

  - 简单的一次性方式`String result = String.format("Hi, %s. Next year, you'll be %d.", "Alice", 30);`

- 格式说明符

  - 更细致的控制间距及对齐方式
  - `%[argument_index$][flags][width][.precision]conversion`
    - **`%`（必须）**：表示格式说明的开始。
    - **`argument_index$`（可选）**：整数值，指定要格式化的参数的位置。例如，`1$` 表示格式化第一个参数。
    - **`flags`（可选）**：控制输出格式的标志，例如 `-` 表示左对齐，`+` 表示输出正负号,`0`表示必要时用0填充。
    - **`width`（可选）**：整数值，表示**最小输出宽度**。如果实际数据宽度小于此值，将会根据标志添加空格或其他字符。
    - **`.precision`（可选）**：用于浮点数和字符串的格式化，指定小数点后的位数或字符串的最大长度。
    - **`conversion`（必须）**：字符，表示要应用的转换类型，如 `d` 代表十进制整数，`s` 代表字符串，`f` 代表浮点数等。
  - 举例
    - `"%1$d, %2$s"`：格式化第一个参数为整数，第二个参数为字符串。
    - `"%-10d"`：左对齐，最小宽度为 10 的整数。
    - `"%+.2f"`：带正负号，保留两位小数的浮点数。
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231117133249357.png" alt="image-20231117133249357" style="zoom: 50%;" />

#### [正则*](..\杂项\正则表达式.md)

- 注意需要是哦那个二倍`\`

  - 如匹配数字`-?\\d+`、符号或正号`-|\\+`

- 可以将正则传入String的split进行拆分（如非字母`\\W`）

  - 也可以作为replace等方法的参数

- 量词

  - 贪婪型：匹配尽可能多的匹配项
  - 勉强型：最小匹配
    - 末尾加?

  - 占有型（Java独有）：在匹配时会尽可能多地捕获字符，并且一旦捕获，**不会放弃匹配的字符**，**即使这可能导致整个模式匹配失败**（不符合后面的条件了）。这与标准的贪婪量词不同，后者在需要时会放弃一部分匹配以使整个模式成功匹配。
    - 末尾加+

- 编译正则表达式

  - `Pattern.compile(String)`
    - 将字符串编译为正则表达式对象

  - 之后通过matcher方法使用Pattern对象`Matcher m = p.matcher(String)`

- pattern标记参数，影响匹配的行为（作为款选的第二个参数）

  - 也可以直接将（）放在正则string前面
  - `Pattern.CASE_INSENSIVE(?i)`忽略USASCII字符集中的大小写
  - `Pattern.COMMENTS(?x)`忽略空白符以及以#开头的注释
  - `Pattern.MULTILINE(?m)`多行模式，开头和结尾变成针对每一行
  - 可以使用|组合标记

- `p.split(CharSequence(待匹配),int limit)`

  - limit表示限制拆分次数，即只进行limit次拆分

- 替换

  - `s.replaceFirst(String pattern, String ss)`

    - `replaceAll`

  - ``` java
    while (matcher.find()) {
        matcher.appendReplacement(sb, "dog");
    }
    //很灵活 matcher.appendReplacement(sb, m.group().toUpperCase()); 可以每次替换时进行一定的处理
    matcher.appendTail(sb);
    System.out.println(sb.toString());
    ```

    - 首先进行匹配操作，通常是在一个循环中使用 `find()` 方法。
    - 对于每个匹配，调用 `appendReplacement(StringBuffer sb, String replacement)` 方法。这个方法会将上次匹配后（或从开始）到这次匹配前的部分追加到 `sb` 中，然后追加 `replacement` 替换的字符串。
    - 完成所有匹配后，可以使用 `appendTail(StringBuffer sb)` 方法将剩余的字符串追加到 `sb` 中。


##### Matcher对象

- find

  - ``` java
    //类似迭代器访问
    while(m.find())
          System.out.print(m.group() + " ");
    //表示搜索的开始位置
    while(m.find(i)) {
          System.out.print(m.group() + " ");
          i++;
        }
    ```

  - matches要求整个字符串与模式匹配

  - lookingAt要求开始部分匹配

- 分组

  - `A(B(C))D`有三个分组`ABCD BC C`
  - `int groupCount()`分组数目（不含分组0）
  - `group(int i=0)`返回前一次匹配操作的第i个分组
  - `int start(int group)`前一次匹配操作中找到的分组的启始索引
  - `public int end(int group)`~最后一个字符的索引+1

- 重置绑定到新的对象`m.reset(String)`

#### 扫描输入

- 读入文本进行分词解析

- 使用SCanner

  - `SCanner stdin =new Scanner(input);`
  - SCanner的构造器支持各种输入类型：File、InputStream、String等
  - 使用nextxx读取并解析各种类型的数据文本
  - `next()`默认读取String类型
    - 还有各种基本类型（不含char），还有**`nextInt()`**、**`nextDouble()`**以及高精度类型等

  - 默认情况下使用空格作为分隔符
    - 自定义分隔符`scanner.useDelimiter("\\s*,\\S*")`使用任意空白符包含的逗号进行分割

- 使用正则扫描

  - ``` java
    public static void main(String [] args) {
        Scanner scanner = new Scanner(threatData);
        String pattern = "(\\d+[.]\\d+[.]\\d+[.]\\d+)@" +
            "(\\d{2}/\\d{2}/\\d{4})";
        while(scanner.hasNext(pattern)) {
            scanner.next(pattern);
            MatchResult match = scanner.match();
            String ip = match.group(1);
            String date = match.group(2);
            System.out.format(
                "Threat on %s from %s%n", date, ip);
        }
    }
    ```

### 数组

- 对于基本类型数组保存值，对象数组保存引用，都必须在创建时进行初始化

  - ``` java
    Object [] a;//未初始化的本地变量
    Object [] b = new Object [5];//引用全部初始化为 null(基本数据类型为 0)，如果需要还需要对数组的每一项及逆行初始化
    Object [] c = {new Object(), new Object()};
    ```

  - 多维数组也可以直接创建`int[][][] a = new int[2][2][4]`

    - 对每一位都单独new可以实现更加自由的数组（长度）生成

- 不能实例化参数化类型数组

  - `Peel<Banana>[] peels = new Peel<Banana>[10];`
  - 可以先创建无参数list再强制转型

#### Arrays工具类

- 数组工具类，用汉语对数组进行各种操作

- `sort`：对数组进行排序。

  - 要求被排序对现象实现Comparable接口（含compareTo）

    - ``` java
      @Override public int compareTo(CompType rv) {
          return (i < rv.i ? -1 : (i == rv.i ? 0 : 1));
        }
      ```

  - 颠倒排序`Arrays.sort(arr,Collections.reverseOrder())`

  - 也可以传入自定义的Comparator

    - ``` java
      class CompTypeComparator
      implements Comparator <CompType> {
        public int compare(CompType o1, CompType o2) {
          return (o1.j < o2.j ? -1 : (o1.j == o2.j ? 0 : 1));
        }
      }
      Arrays.sort(a, new CompTypeComparator());
      ```

- `binarySearch`：在已排序的数组中进行二分搜索。

  - 如果没有找到会返回负数，表示`-(insertion point -1)`即知识应该插入的位置（比目标值大的第一个元素的索引值）
  - 必要时传入Comparator
  - `Arrays.binarySearch(arr,n)`

- `toString`：生成数组内容的字符串表示。

  - `deepToString`生成多维数组的表示

- `asList`：将数组转换为`List`。

- `copyOf(arr,len,[target.class])`按新的长度创建已有数组的副本（深拷贝）

- `copyOfRange(arr,from,to,[target.class])`对指定范围创建副本

- `equals(a,b)`用于比较两个对象是否在概念上相等。其默认行为是比较对象引用，但通常被重写以比较对象的内部状态。

- `deepEquals`用于深度(递归)比较两个对象。它特别适用于比较数组或复杂的嵌套对象，可以递归地比较对象的内部元素。（可以用与多维数组）

- `Arrays.deepToString(list)`用于对数组打印输出

- `Arrays.fill(arr,[from,to],content)`对数组进行填充

- `Arrays.setAll(arr,lambda)`可以通过下标填充数组生成元素

  - list有对应的`replaceAll()`方法

  - ``` java
    Arrays.setAll(ia, n -> n); 
    Arrays.setAll(ca, SimpleSetAll:: getChar);
    ```

  - 除了初始化也可以用于对数组的每个元素进行一定的操作和修改
  - 并行版本`parallelSetAll`在多个线程上并行执行，适用于大型数组或计算密集型的赋值操作。

### 字面量

- 进制表示

  - 十六进制`0x2f`或`0X2F`

  - 八进制`0177`（前置0）
  - 二进制`0b0101`或`0B0101`
    - `Integer.toBinaryString()`

- 类型后缀
  - long：`l L`
  - float：`f F`
  - double：`d D`
- 下划线
  - 为了便于阅读可以用下划线划分数字`111_111_111`
  - 开头结尾，前后缀相邻位置不能使用，也不能连续使用
- 科学计数法
  - `1e-43`
  - 默认会生成double类型，可以手动添加f

### 补充

- 自动类型推断（JDK11）
  - 使用`var`关键字
  - 必须为变量提供初始值才能进行推断
  - 不允许作为返回值

## 作用域

- Java不允许重复定义变量（即使是在内层作用域），也就是不能在内层重新定义并隐藏外层

- ``` java
  {
  	String s = new Sring("");
  }
  ```

  - 引用s会在作用域结束后消失（像基本类型那样）
  - 创建的对象由Java垃圾回收机制控制回收

## 运算

- 比较：
  - 对于基本类型直接使用`= !=`
  - 对于非基本类型使用`=`会直接比较引用是否相同
  - 内置类型可以直接使用`equals()`进行比较
  - 非基本类型默认`equals()`也是比较引用，需要重写方法

- 逻辑运算
  - 参与逻辑运算的变量必须是布尔类型

- 位移
  - 数学位移`>>`
  - 逻辑位移`>>>`

## 流程控制

- for-in

  - ``` java
    for(type element: array)
    {
        System.out.println(element);
    }
    ```

- 标签

  - 标签应该放置在迭代语句之前

    - ``` java
      label:
      xxx{
          {
              break label;
          }
      }
      ```

  - 控制break、continue控制特定的迭代（比如直接从内层break到最外层）

  - 应用于嵌套循环

- switch

  - 可以用字符串、整数作为选择


### 方法

- `public static void(){}`静态方法是类方法，可以通过**类名**直接调用，不能被子类重写，但可以被同名函数覆盖

- 静态代码块，内部全部为静态

- ``` java
  static{
  
  }
  ```

- 静态方法不能访问实例变量或实例方法，只能访问静态变量和其他静态方法。

- 方法与方法之间是平级关系，不能嵌套定义

- 可变参数

  - 必须是最后一个参数` public static void printMax( double... numbers)`使用数组类型也可以
  - 可以将传入的多个参数自动转化为一个数组
  - 允许传入0个元素

## 对象的传递与返回

- 默认的传递方式都是引用传递，要注意其副作用的影响
- 引用有作用域，但是对象没有

### 创建副本

- 克隆对象

  - 需要将想要克隆的子类中将`clone()`方法重写为public（默认为protected）
  - clone的返回值是Object因此还需要进行显式类型转换
  - 要注意的是有时clone并不总是符合预期，如对ArrayList进行clone并不会对内部的元素进行复制
    - 直接赋值二进制数据，因此把引用复制了过来
    - 包装数据类型是例外，包装数据类型不可变，修改后会创建新对象，因此使用clone后不会再同步修改

- 通常除了修改访问控制为public外调用`super.clone()`

- Cloneable接口，用于验证一个对象是否具有克隆能力，如果一个类没有实现该接口，在使用clone方法时会报错

  - 由于会引发一场，因此需要trycatch

  - ``` java
    class Duplo implements Cloneable {
      private int n;
      Duplo(int n) { this.n = n; }
      @Override public Duplo clone() {    
        try {
            //对于大部分对象的处理
          return (Duplo)super.clone();
        } catch(CloneNotSupportedException e) {
          throw new RuntimeException(e);
        }
      }
      public int getValue() { return n; }
      public void setValue(int n) { this.n = n; }
      public void increment() { n++; }
      @Override public String toString() {
        return Integer.toString(n);
      }
    }
    ```

- 克隆组合对象

  - 除了克隆自身外要对所有引用对象进行克隆和重新绑定

  - ``` java
    @Override public OceanReading clone() {
        OceanReading or = null;
        try {
            or =(OceanReading)super.clone();
        } catch(CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
        // 克隆并重新绑定引用
        or.depth = (DepthReading)or.depth.clone();
        or.temperature =(TemperatureReading)or.temperature.clone();
        return or;
    }
    ```

- 深拷贝ArrayList

  - 先cone集合，之后对每一项进行clone和重新绑定到集合

- 通过序列换进行深拷贝

  - 序列化再反序列化实现拷贝（但是这很慢）

  - 需要实现`Serializable`

  - ``` java
    import java.io.*;
    
    public class DeepCopy {
    
        @SuppressWarnings("unchecked")
        public static <T extends Serializable> T deepCopy(T object) {
            try {
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                try (ObjectOutputStream oos = new ObjectOutputStream(baos)) {
                    oos.writeObject(object);
                }
    
                ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
                try (ObjectInputStream ois = new ObjectInputStream(bais)) {
                    return (T) ois.readObject();
                }
            } catch (IOException | ClassNotFoundException e) {
                throw new RuntimeException(e);
            }
        }
    }
    
    ```

### 控制可克隆性

- 一个类将clone重写为了public，那么所有的子类都可以clone了，因为无法降低基于基类的方法在子类中的可访问性
- 支持clone：重写clone并实现接口
- 不实现接口：不允许直接克隆但是可以继承该类并在子类中实现克隆
  - 或者在clone中重写并抛出异常禁止使用
  - 但是如果子类没有遵守调用super.clone这种限制会失效
- 类设置为final，实现完全禁止克隆（可以与构造器设置为private配合，实现限制对象的数目）

## 面向对象

### 主类&程序入口

- 文件中必须存在与文件名相同的类
- 入口函数`public static void main(String[] args){}`
  - 多个类都可以有main，但是只有主类的会被自动调用

- 每个java文件中只能有一个public类，也只有这一个类可以在包外访问
  - 即其他的类适用于支撑唯一的public类的


### 思想

- 数据操作分离：实体类只负**责数据存取**（get/set），而对数据的处理交给其他类来完成，以实现**数据和数据业务**处理相分离

### 访问权限控制

- public
- private：仅类内访问
- default：包内可见（即不添加任何修饰符）
- protected：子类可访问，即使不在一个包，同时也提供了包访问权限
- 在只有包访问权限的类中使用publicc构造方法是没有意义的
- 类的访问权限
  - 类中可以不存在public类，此时文件可以随意命名
  - 只有默认（包），和public两种作用域


### 构造

- 字段初始化：字段初始化是在对象创建时，即在**构造函数之前**执行的。

  - 从上到下的顺序进行初始化

  - 可以直接使用常量对成员变量进行初始化
  - 也可以使用函数进行初始化
    - 注意如果函数有参数则参数必须是已经初始化的

- 实例初始化块：实例初始化块是在对象创建时，**在字段初始化之后**但在**构造函数之前**执行的。

  - **静态初始化块会优先执行**，只能初始化静态变量，在类**初次加载**时执行（创建对象/访问静态成员）

  - **普通初始化块按顺序执行**，实例化时执行

  - 会从父类到子类依次执行

    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115026923.png" alt="image-20230921115026923" style="zoom:50%;" />

  - ``` java
    class A{
        // 静态初始化块
        static {
            System.out.println("static init block A");
        }
        // 普通初始化块
        {
            System.out.println("common init block A");
        }
        // 构造方法
        public A() {
            System.out.println("Constructor without params A");
        }
    }
    ```

- 初始化顺序

  - ``` java
    class Bowl {
      Bowl(int marker) {
        System.out.println("Bowl(" + marker + ")");
      }
      void f1(int marker) {
        System.out.println("f1(" + marker + ")");
      }
    }
    
    class Table {
      static Bowl bowl1 = new Bowl(1);
      Table() {
        System.out.println("Table()");
        bowl2.f1(1);
      }
      void f2(int marker) {
        System.out.println("f2(" + marker + ")");
      }
      static Bowl bowl2 = new Bowl(2);
    }
    
    class Cupboard {
      Bowl bowl3 = new Bowl(3);
      static Bowl bowl4 = new Bowl(4);
      Cupboard() {
        System.out.println("Cupboard()");
        bowl4.f1(2);
      }
      void f3(int marker) {
        System.out.println("f3(" + marker + ")");
      }
      static Bowl bowl5 = new Bowl(5);
    }
    
    public class StaticInitialization {
      public static void main(String [] args) {
        System.out.println("main creating new Cupboard()");
        new Cupboard();
        System.out.println("main creating new Cupboard()");
        new Cupboard();
        table.f2(1);
        cupboard.f3(1);
      }
      static Table table = new Table();
      static Cupboard cupboard = new Cupboard();
    }
    /* Output:
    Bowl(1)
    Bowl(2)
    Table()
    f1(1)
    Bowl(4)
    Bowl(5)
    Bowl(3)
    Cupboard()
    f1(2)
    main creating new Cupboard()
    Bowl(3)
    Cupboard()
    f1(2)
    main creating new Cupboard()
    Bowl(3)
    Cupboard()
    f1(2)
    f2(1)
    f3(1)
    */
    ```

  - **静态字段的初始化**：首先，类`Table`和`Cupboard`的静态字段在首次被访问时初始化。在`StaticInitialization`的`main`方法执行之前，会初始化**这两个类的静态字段**，因为它们在`main`方法中被用作静态字段。(初始类之前要先对其的静态成员进行初始化)

    - `Table.bowl1`初始化，打印`Bowl(1)`。
    - `Table.bowl2`初始化，打印`Bowl(2)`。
    - `Cupboard.bowl4`初始化，打印`Bowl(4)`。
    - `Cupboard.bowl5`初始化，打印`Bowl(5)`。

  - **`Table`和`Cupboard`的实例初始化**：之后，`StaticInitialization`中的静态字段`table`和`cupboard`被初始化。

    - 当初始化table时：
      - 调用`Table`的构造函数，打印`Table()`。
      - 在构造函数中调用`bowl2.f1(1)`，打印`f1(1)`。
    - 当初始化cupboard时：
      - `Cupboard.bowl3`初始化，打印`Bowl(3)`（这是一个实例字段，所以在每次创建`Cupboard`实例时都会初始化）。
      - 调用`Cupboard`的构造函数，打印`Cupboard()`。
      - 在构造函数中调用`bowl4.f1(2)`，打印`f1(2)`。

  - **执行`main`方法**：

    - 打印`main creating new Cupboard()`。
    - 创建新的Cupboard实例：
      - `Cupboard.bowl3`初始化，打印`Bowl(3)`。
      - 调用`Cupboard`的构造函数，打印`Cupboard()`。
      - 在构造函数中调用`bowl4.f1(2)`，打印`f1(2)`。
    - 打印`main creating new Cupboard()`。
    - 再次创建新的Cupboard实例：
      - `Cupboard.bowl3`初始化，打印`Bowl(3)`。
      - 调用`Cupboard`的构造函数，打印`Cupboard()`。
      - 在构造函数中调用`bowl4.f1(2)`，打印`f1(2)`。
    - 调用`table.f2(1)`，打印`f2(1)`。
    - 调用`cupboard.f3(1)`，打印`f3(1)`。

  - ``` java
    class Cup {
      Cup(int marker) {
        System.out.println("Cup(" + marker + ")");
      }
      void f(int marker) {
        System.out.println("f(" + marker + ")");
      }
    }
    
    class Cups {
      static Cup cup1;
      static Cup cup2;
      static {
        cup1 = new Cup(1);
        cup2 = new Cup(2);
      }
      Cups() {
        System.out.println("Cups()");
      }
    }
    
    public class ExplicitStatic {
      public static void main(String [] args) {
        System.out.println("Inside main()");
        Cups.cup1.f(99);                  // [1]
      }
      static Cups cups1 = new Cups();  // [2]
      static Cups cups2 = new Cups();  // [2]
    }
    /* Output:
    Cup(1)
    Cup(2)
    Cups()
    Cups()
    Inside main()
    f(99)
    */
    ```

  - 首先对ExplicitStatic的静态成员cups1、cups2进行初始化

  - 调用静态初始化对cup1、cup2进行初始化

  - 调用构造函数对Cups进行初始化

  - 执行main函数

- 自定义了有参构造函数，系统不会提供无参构造函数

  - 使用new一个数组时，如果没有无参构造函数，会被初始化为null，需要再手动使用有参构造函数进行初始化

  - ``` java
    Person [] people = new Person [10];
    for (int i = 0; i < people.length; i++) {
        people [i] = new Person("John Doe", 30);
    }
    ```

- 基本类型的自动化转化，传入int类型参数，如果只有long参数的函数，则会自动进行宽化，但是不会自动窄化（必须显式处理）

- 在构造函数中调用构造函数

  - `this(目标构造函数参数)`
  - 只能在构造函数中调用

- 存在继承的构造

  - 默认只会调用父类的无参构造函数，如果需要有参构造函数需要显式调用`super(xx)`

  - 父类->成员（按照声明顺序进行初始化）->自身

  - ``` java
    public class Test {
        public static void main(String [] args) {
            new SubClass();
        }
    }
    class SuperClass {
        static int staticSuperVar = initStaticSuperVar();
        int instanceSuperVar = initInstanceSuperVar();
    
        static {
            System.out.println("Static initializer of SuperClass");
        }
    
        {
            System.out.println("Instance initializer of SuperClass");
        }
    
        SuperClass() {
            System.out.println("Constructor of SuperClass");
        }
    
        static int initStaticSuperVar() {
            System.out.println("Static variable of SuperClass");
            return 0;
        }
    
        int initInstanceSuperVar() {
            System.out.println("Instance variable of SuperClass");
            return 0;
        }
    }
    
    class SubClass extends SuperClass {
        static int staticSubVar = initStaticSubVar();
        int instanceSubVar = initInstanceSubVar();
    
        static {
            System.out.println("Static initializer of SubClass");
        }
    
        {
            System.out.println("Instance initializer of SubClass");
        }
    
        SubClass() {
            System.out.println("Constructor of SubClass");
        }
    
        static int initStaticSubVar() {
            System.out.println("Static variable of SubClass");
            return 0;
        }
    
        int initInstanceSubVar() {
            System.out.println("Instance variable of SubClass");
            return 0;
        }
    }
    /*
    Static variable of SuperClass
    Static initializer of SuperClass
    Static variable of SubClass
    Static initializer of SubClass
    Instance variable of SuperClass
    Instance initializer of SuperClass
    Constructor of SuperClass
    Instance variable of SubClass
    Instance initializer of SubClass
    Constructor of SubClass
    */
    ```

  - 

### 垃圾收集

- 垃圾处理器会自动释放new分配的内存

- finalize

  - 在对象被垃圾收集器析构(回收)之前调用，并且会在下一次垃圾收集时再回收这个对象占用的内存（但也有可能不会被垃圾收集，比如内存没有被耗尽）


  - ```
    protected void finalize()
    {
       // 在这里终结代码
    }
    ```

  - finalize的操作应该只与内存及释放相关
    - 比如使用非Java方法分配的内存就需要手动进行释放


  - Java 的内存回收可以由 JVM 来自动完成。如果你手动使用，则可以使用上面的方法。


  - **已经弃用**

- 如果需要对类进行清理，应该自己创建清理方法并显式调用

- 清理顺序

  - 先子类后父类

### 继承

- `public class Zi extends Fu{} `
- 多态

  - 对象多态 : 将方法的形参定义为父类类型, 这个方法可以接收该父类的任意子类对象 
  - 行为多态 : 同一个行为, 具有多个不同表现形式或形态的能力
  - 多态的好处：提高了程序的扩展性
  - 多态的弊端：不能使用子类的特有成员、
- 判断一个对象是否是一个类的实例`对象名 instanceof 类型`返回布尔值

### 抽象类与抽象方法

- 抽象方法：`public abstract void`

- 抽象类：存在抽象方法的类必须**声明为抽象类**

  - ``` java
    public abstract class Fu {
        public abstract void 行善();
    }
    ```

  - 抽象类不能实例化，抽象类的子类要么重写抽象类中所有抽象方法，要么也是抽象类

### final关键字

- 用于数据
  - 声明常量
  - 永远不会改变的编译时常量
  - 对于基本数据类型为静态，对于其他对象类型为动态（绑定引用，但是被引用的对象是可以修改的）
  - 空白final：
    - 没有初始值的final字段
    - 编译器会确保在使用前进行了初始化
    - 第一次赋值之后就不再可以变化
  - final参数
    - 可以将方法参数设置为final类型
- 用于方法
  - 禁止方法被重写
  - （private方法本质上是final的）
  - 并不是不能在子类中写同名函数，知识不再具有重写关系，只是创建了一个新方法(使用@Override则会报错指出问题)
  - 向上转型后不能使用同名方法（动态绑定）
- 用于类
  - 禁止被继承
  - final类的方法自然是final（不需要写出来）

### 组合与聚合

- **组合**强合成：“部分”的生命期不能比“整体”还要长
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115727663.png" alt="image-20230921115727663" style="zoom:50%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115826475.png" alt="image-20230921115826475" style="zoom: 50%;" />
- **聚合**、关联弱合成：部分可以独立存在
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230921115848748.png" alt="image-20230921115848748" style="zoom:50%;" />

### 多态

- private方法不能动态绑定
- 对变量重写不是动态绑定，必须是对方法重写
- 静态方法不能动态绑定

- 不应该在构造函数中使用动态绑定：构造函数被调用时子类还没有完全初始化，此时通过动态绑定去调用子类的方法可能存在问题（甚至成员变量都还没有赋初值进行初始化！
- 子类中重写方法的返回值可以是基类方法返回值的子类型

### 向下转型与反射

- 向上转型会丢失特定**类型的信息**，通过向下转型可以重新获取类型信息
- java中所有的转型都会被检查，运行时会检查强制转型是否正确（会抛出异常），这也是Java反射的一部分
- 

### 重写运算

- 比较

  - ``` java
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

- 如飞船继承发动机并不合理，因此使用组合并公开方法

- ``` java
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

- 

### 接口

- 体现的思想是对规则的声明

- 接口不能实例化，接口和类是实现关系，实现接口的类实现接口
  - `private Linable linable;`其中Linable是一个接口，作为类的成员变量
  - 接口不能直接实例化，它只能通过实现该接口的类来创建对象，因此上面并没有进行实例化，对于该接口的实例化应该在构造函数或其它方法中进一步完成。
  - 接口是对类的一种形容词，而抽象类是类层次结构的一部分
  
- 语法
  - `interface 接口名 {} `
  - `public class 类名 implements 接口名 {}`
  - **一个类可以继承一个类的同时实现多个接口**
  - 接口之间可以单继承也可以多继承
    - 有重复方法也无所谓，因为就是都实现了，不存在冲突
  
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230920230013866.png" alt="image-20230920230013866" style="zoom:50%;" />

  - 接口中的成员变量实际上是public final static的静态常量，按照命名规范应该使用全大写字母以及_划分

- 默认方法：`public default void show() {}`加上default关键字后就不是抽象方法了，**可以不重写**，如果要重写只需要在重写时去掉default，如果继承的多个接口中有相同的声明则必须重写

  - 使用具有默认实现的方法在继承多个接口时可能产生一些多继承的问题
  - 如果两个接口有完全相同的成员（参数必须相同），并且没有重写方法，则会报错，要求必须重写
    - 当然也可以使用super直接选一个

  - 对于重名的常量则不会有问题，因为类不会从接口继承常量，常量必须通过接口名进行显式访问
  - 此时可以作为任意一种接口类型的参数使用（向上转型）

- 实现接口中的方法必须显式声明为public（默认也会是public）

- 函数式接口
  - 接口内部只有一个抽象方法的形式（类似c++中函数指针，表示一个函数）
  - 可以使用@FunctionalInterface标记必须满足函数式接口

- public接口像类一样，只能出现在相同名称的文件

- 接口中的静态方法

  - 在接口包含逻辑上属于它的实用工具
  - 接口的静态方法必须通过接口本身进行调用（这与常量成员是相同的）

- 接口-完全解耦

  - 当方法与类而不是接口配合使用时，只能使用**该类或子类**，而不能使用不在这个继承层次结构上的类（即使具有同样的方法）

  - 但如果使用接口作为变量类型限制（比如策略模式）

    - ``` java
      //接口的定义
      package interfaces.interfaceprocessor;
      
      public interface Processor {
        default String name() {
          return getClass().getSimpleName();
        }
          //需要实现的方法
        Object process(Object input);
      }
      //使用接口进行操作
      package interfaces.interfaceprocessor;
      
      public class Applicator {
        public static void apply(Processor p, Object s) {
          System.out.println("Using Processor " + p.name());
          System.out.println(p.process(s));
        }
      }
      //一些实现了接口的字符串操作
      package interfaces.interfaceprocessor;
      import java.util.*;
      
      interface StringProcessor extends Processor {
          //返回值协变
        @Override
        String process(Object input);        // [1]
        String S =                           // [2]
        "If she weighs the same as a duck, " +
        "she's made of wood";
        static void main(String [] args) {    // [3]
          Applicator.apply(new Upcase(), S);
          Applicator.apply(new Downcase(), S);
          Applicator.apply(new Splitter(), S);
        }
      }
      
      class Upcase implements StringProcessor {
        @Override // Covariant return:
        public String process(Object input) {
          return ((String)input).toUpperCase();
        }
      }
      
      class Downcase implements StringProcessor {
        @Override
        public String process(Object input) {
          return ((String)input).toLowerCase();
        }
      }
      
      class Splitter implements StringProcessor {
        @Override
        public String process(Object input) {
          return Arrays.toString(((String)input).split(" "));
        }
      }
      ```

  - 适配器

    - 有时不方便（无法）对类进行修改，使用适配器，将已有的接口转化为需要的接口，也可以为不满足接口的类添加方法，使得满足接口的要求

    - ``` java
      package interfaces.interfaceprocessor;
      import interfaces.filters.*;
      
      class FilterAdapter implements Processor {
          //要适配的对象
        Filter filter;
        FilterAdapter(Filter filter) {
          this.filter = filter;
        }
        @Override
        public String name() { return filter.name(); }
        @Override
        public Waveform process(Object input) {
            //转化为接口需要的
            //由此实现从 Filter 接口转化到需要的 Processor 接口
          return filter.process((Waveform)input);
        }
      }
      
      public class FilterProcessor {
        public static void main(String [] args) {
          Waveform w = new Waveform();
          Applicator.apply(
            new FilterAdapter(new LowPass(1.0)), w);
          Applicator.apply(
            new FilterAdapter(new HighPass(2.0)), w);
          Applicator.apply(
            new FilterAdapter(new BandPass(3.0, 4.0)), w);
        }
      }
      ```

- 接口可以继承，向接口中添加新的方法生成

  - 接口之间是可以多继承的（即extends后面跟多个）
  - 注意多继承的接口中不应有重名，参数相同但是返回值不同的方法

- 接口的定义同样可以前台在其他类/接口中

  - private接口允许嵌套在类内
  - 类内实现private接口的类可以为public，但是禁止向上转型
  - public方法返回private接口的引用也会被禁止使用


- 接口的private方法JDK9
  - private表示只想自己使用的方法
  - private方法也是default的
- 密封类和密封接口JDK17
  - 基类和接口可以限制自己能派生哪些类型
  - 在基类/接口中指出允许的子类`sealed class Base permits D1, D2{}`
  - 如果表示只让同一文件存在子类，只需要使用`sealed`即可
  - 必须至少有一个子类
  - 只是限制直接子类，如果直接子类使用`non-sealed`则可以有任意间接子类
  - 常用于switch，可以免default

### 内部类

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230920231026654.png" alt="image-20230920231026654" style="zoom: 50%;" />

  - 实例`Outer.Inner in = new Outer().new Inner();`
  - 必须有外部类的对象来创建内部类，因为内部类创建时会自动进行绑定

- 内部类可以声明为public、private、protected、default

- 内部类中, 访问外部类成员 : 直接访问, **包括私有**（无论嵌套多少层都可以透明的访问，创建时也需要逐级`.`）

  - 通常直接访问就可以，如果需要显示访问：格式`Outer.this.`

  - 使用内部类创建迭代器

    - ``` java
      interface Selector {
        boolean end();
        Object current();
        void next();
      }
      
      public class Sequence {
        private Object [] items;
        private int next = 0;
        public Sequence(int size) {
          items = new Object [size];
        }
        public void add(Object x) {
          if(next < items.length)
            items [next++] = x;
        }
          //提供序列化迭代器实现对外部类的访问
        private class SequenceSelector implements Selector {
          private int i = 0;
          @Override
          public boolean end() { return i == items.length; }
          @Override
          public Object current() { return items [i]; }
          @Override
          public void next() { if(i < items.length) i++; }
        }
        public Selector selector() {
          return new SequenceSelector();
        }
        public static void main(String [] args) {
          Sequence sequence = new Sequence(10);
          for(int i = 0; i < 10; i++)
            sequence.add(Integer.toString(i));
            //获取一个用于访问元素的迭代器
          Selector selector = sequence.selector();
          while(! selector.end()) {
            System.out.print(selector.current() + " ");
            selector.next();
          }
        }
      }
      ```

    - 这里的内部类是private的，其实例和类型对外部类之外都是不可见的，外部类不能直接用public方法返回一个preivate内部类的实例，但是可以返回内部类实现的public基类或接口（向上转型Selector）**这种方式隐藏了内部类的具体实现，只暴露了接口或超类的公共方法。**

    - 将内部类设置为private或protected可以阻止外部直接访问，同时**只提供向上转型的结果**实现更好的封装

- 局部内部类

  - 在方法作用域中创建内部类

  - ``` java
    public class Parcel5 {
      public Destination destination(String s) {
          //在放回方法中定义一个内部类，这个内部类只能在该方法作用域中使用，他的定义不能在外部使用，但是可以向上转型后作为返回值返回
        final class PDestination implements Destination {
          private String label;
          private PDestination(String whereTo) {
            label = whereTo;
          }
          @Override
          public String readLabel() { return label; }
        }
        return new PDestination(s);
      }
      public static void main(String [] args) {
        Parcel5 p = new Parcel5();
        Destination d = p.destination("Tasmania");
      }
    }
    ```

- 匿名内部类:定义一个类的同时对其**进行实例化**

  - 匿名类只能**扩展一个类或实现一个接口**

  - ``` java
    public class Parcel7 {
      public Contents contents() {
        return new Contents() { // Insert class definition
          private int i = 11;
          @Override public int value() { return i; }
        }; // Semicolon required
      }
      public static void main(String [] args) {
        Parcel7 p = new Parcel7();
        Contents c = p.contents();
      }
    }
    ```

    - 创建了一个继承自Contents的匿名类对象，返回会自动向上转型

  - 也可以通过构造函数传参，可以用于调用超类的构造函数

    - ``` java
      public class Parcel8 {
        public Wrapping wrapping(int x) {
          // Base constructor call:
          return new Wrapping(x) {          // [1]
            @Override public int value() {
              return super.value() * 47;
            }
          };                                // [2]
        }
        public static void main(String [] args) {
          Parcel8 p = new Parcel8();
          Wrapping w = p.wrapping(10);
        }
      }
      
      public class Wrapping {
        private int i;
        public Wrapping(int x) { i = x; }
        public int value() { return i; }
      }
      ```

  - 如果要在匿名类使用匿名类外的对象，要求参数用final修饰，或者保证初始化后就不会再变化

  - 匿名函数的构造器

    - 可以使用初始化块`{}`

  - ``` java
    public class Parcel10 {
      public Destination
          //被使用的对象要保证为 final
      destination(final String dest, final float price) {
        return new Destination() {
          private int cost;
          // 初始化块
          {
            cost = Math.round(price);
            if(cost > 100)
              System.out.println("Over budget!");
          }
          private String label = dest;
          @Override
          public String readLabel() { return label; }
        };
      }
      public static void main(String [] args) {
        Parcel10 p = new Parcel10();
        Destination d = p.destination("Tasmania", 101.395F);
      }
    }
    ```

  - 

- 静态内部类（嵌套类），内部类使用static修饰，

  - 不需要内部类对象和外部类对象之间的连接
  - 不需要通过外部类对象创建
  - 无法从内部访问**非static**的外部对象
  - 嵌套类内可以有static的数据及字段          
  - `Outer.Inner in = new Outer.Inner();`
  - 可以定义在接口中，默认就是public static的
  - 静态内部类不是真的“静态”，只是不依赖于外部类
    - 也可以有非静态方法，并且可以创建多个实例
    - 静态内部来与外部顶层类是类似的，只是定义在一个类的内部


#### 应用

- 间接实现多继承

  - 每个内部类都有可以独立地继承自一个实现

  - 可以使用每个内部类去继承一个类，间接实现多继承

    - ``` java
      class D {}
      abstract class E {}
      
      class Z extends D {
        E makeE() { return new E() {}; }
      }
      
      public class MultiImplementation {
        static void takesD(D d) {}
        static void takesE(E e) {}
        public static void main(String [] args) {
          Z z = new Z();
          takesD(z);
          takesE(z.makeE());
        }
      }
      ```

- 闭包与回调

  - ``` java
    interface Incrementable {
      void increment();
    }
    
    //直接实现接口
    class Callee1 implements Incrementable {
      private int i = 0;
      @Override public void increment() {
        i++;
        System.out.println(i);
      }
    }
    //实现方法，但没有实现接口
    class MyIncrement {
      public void increment() {
        System.out.println("Other operation");
      }
      static void f(MyIncrement mi) { mi.increment(); }
    }
    
    //使用内部类实现接口，并给出方法用于获取内部接口（这里的内部接口会对外部进行访问，是一个闭包）
    class Callee2 extends MyIncrement {
      private int i = 0;
      @Override public void increment() {
        super.increment();
        i++;
        System.out.println(i);
      }
      private class Closure implements Incrementable {
        @Override public void increment() {
          // Specify outer-class method, otherwise
          // you'll get an infinite recursion:
          Callee2.this.increment();
        }
      }
      Incrementable getCallbackReference() {
        return new Closure();
      }
    }
    //初始化时传入接口的实现，用于回调
    class Caller {
      private Incrementable callbackReference;
      Caller(Incrementable cbh) {
        callbackReference = cbh;
      }
      void go() { callbackReference.increment(); }
    }
    
    public class Callbacks {
      public static void main(String [] args) {
        Callee1 c1 = new Callee1();
        Callee2 c2 = new Callee2();
        MyIncrement.f(c2);
        Caller caller1 = new Caller(c1);
        Caller caller2 =
          new Caller(c2.getCallbackReference());
        caller1.go();
        caller1.go();
        caller2.go();
        caller2.go();
      }
    }
    ```

  - 闭包：允许方法**保留和使用定义在其外部作用域的变量**，即使外部方法已经完成执行。`Callee2`的内部类`Closure`类似于一个闭包，因为它封装了`Callee2`的环境并提供了对其`increment`方法的访问。

    - **闭包让你可以在一个内层函数中访问到其外层函数的作用域。**

  - 回调：一个方法**接受另一个方法作为参数**，然后在适当的时候执行传递。`Caller`类接受一个实现了`Incrementable`接口的对象作为回调，并在`go`方法中调用该回调对象的`increment`方法。

- 内部类编译后会产生单独的class文件，如`外部类$内部类.class`

## 泛型

- 基本类型不能作为泛型参数

- 泛型类
  
  - ``` java
    public class Position <T>{
        private T sth;
    }
    ```
  
- 泛型方法

  - ``` java
    public class GenericMethods {
        public <T> void f(T x) {
            System.out.println(x.getClass().getName());
        }
    }
    ```

  - 通常来说使用泛型方法时不需要手动指出参数类型，可以自动推断
  - `public <A,B> Tuple2<A,B>tuple(A a,B b)`
    - 第一个<>是方法的参数，在调用时可以自动推断
    - 第二个<>是返回值的参数，应该指出`Tuple2`表示为参数化的对象，类似一种向上转型

- 泛型接口

  - 生成器模式，`Spplier<T>`，要求重写实现`public T get(){}`

  - ``` java
    public class Fibonacci implements Supplier <Integer> {
      private int count = 0;
      @Override
      public Integer get() { return fib(count++); }
      private int fib(int n) {
        if(n < 2) return 1;
        return fib(n-2) + fib(n-1);
      }
      public static void main(String [] args) {
        Stream.generate(new Fibonacci())
          .limit(18)
          .map(n -> n + " ")
          .forEach(System.out:: print);
      }
    }
    
    //通过适配器再实现可迭代
    public class IterableFibonacci
    extends Fibonacci implements Iterable <Integer> {
      private int n;
      public IterableFibonacci(int count) { n = count; }
      @Override public Iterator <Integer> iterator() {
        return new Iterator <Integer>() {
          @Override
          public boolean hasNext() { return n > 0; }
          @Override public Integer next() {
            n--;
              //内部类访问外部类对象
            return IterableFibonacci.this.get();
          }
          @Override
          public void remove() { // Not implemented
            throw new UnsupportedOperationException();
          }
        };
      }
      public static void main(String [] args) {
        for(int i : new IterableFibonacci(18))
          System.out.print(i + " ");
      }
    }
    ```

- 扁平化多级集合

  - ``` java
    class Shelf extends ArrayList <Product> {
      Shelf(int nProducts) {
        Suppliers.fill(this, Product.generator, nProducts);
      }
    }
    
    class Aisle extends ArrayList <Shelf> {
      Aisle(int nShelves, int nProducts) {
        for(int i = 0; i < nShelves; i++)
          add(new Shelf(nProducts));
      }
    }
    ```

- 基本类型不能作为泛型参数，需要使用包装类型

- 一个类不能同时实现一个泛型接口的两种变体（不同泛型参数的泛型接口），因为他们在类型擦除后实际上是相同的

### 类型擦除

- 类型擦除

  - `ArrayList<String>.class==ArrayList<Integer>`

  - Java泛型内部不存在有关**泛型参数类型**的信息

  - java确定T的类型并不是通过T本身，也不是传入的参数（只是会检查一下），而是通过extend等限制的范围确定

    - ``` java
      class Manipulator <T> {
        private T obj;
        Manipulator(T x) { obj = x; }
        // Error: cannot find symbol: method f():
        public void manipulate() { obj.f(); }
      }
      
      public class Manipulation {
        public static void main(String [] args) {
          HasF hf = new HasF();
          Manipulator <HasF> manipulator =
            new Manipulator <>(hf);
          manipulator.manipulate();
        }
      }
      //java 中只知道是一个未知的 T（会被认为是 Object），并不知道具体是什么，因此不认为有 f
      class Manipulator2 <T extends HasF> {
        private T obj;
        Manipulator2(T x) { obj = x; }
        public void manipulate() { obj.f(); }
      }
      //这样就知道是 HasF 了！
      ```

    - ``` c++
      template <class T> class Manipulator {
        T obj;
      public:
        Manipulator(T x) { obj = x; }
        void manipulate() { obj.f(); }
      };
      
      class HasF {
      public:
        void f() { cout << " HasF:: f()" << endl; }
      };
      
      int main() {
        HasF hf;
        Manipulator <HasF> manipulator(hf);
        manipulator.manipulate();
      }
      ```

    - 而c++是可以的，c++泛型内部知道自己的类型，会进行编译检查

- java的字节码中实际上没有泛型，是将类型参数用**边界类型替换(类型替换)**

  - 比如无限制的泛型\<T>**会替换为Object**，而有限制的\<T extends X>会被替换为X

  - 也就是说T类型并不是真正存在的，**不能直接使用T进行实例**,使用T作为参数`class<T>`也是存在问题的

  - 但是**T作为返回值时可以正确处理，会返回传入的类型**

    - ``` c++
      @SuppressWarnings("unchecked")//不显示强制转化的警告
        T [] create(int size) {
          return (T [])Array.newInstance(kind, size);//kind 实际相当于没有参数 Class 即 Object 因此要强制转化类型
        }
      ```

  - ``` java
    public class Holder <T> {
        private T obj;
        public void set(T obj){ this.obj = obj; }
        public T get(){ return obj; }
        public void testT(Object arg){
            if (arg instanceof T){ ... } //编译错误
            T var = new T(); //编译错误
            T [] array = new T [100]; //编译错误
            }
        }
    }
    ```

  - 编译时检查

    - ``` java
      public class Holder <T> {
          private T obj; //在编译时，该类中的所有的 T 都会被替换为边界类型 Object。
          public void set(T obj){ this.obj = obj; }
          public T get(){ return obj; }
          public static void main(String [] args){
              Holder <Integer> holder = new Holder <>();
              //编译器会检查实参是不是一个 Integer，
              //虽然这里的 1 是 int 类型，但是因为自动包装机制的存在，
              //他会被转化为一个 Integer，因此能够通过类型检查。
              holder.set(1); 
              //编译器也会进行类型检查，
              //并且自动插入一个 Object 类型到 Integer 类型的转型操作。
              Integer obj = holder.get();
          }       
      }
      ```

- 对泛型的处理全部集中在**编译**期，在编译时，编译器会执行如下操作。

  - 会将泛型类的类型参数都用**边界类型**替换。
  - 对于传入对象给方法形参的指令，编译器会执行一个**类型检查**（这是与直接使用Object相比最大的意义了），看传入的对象是不是类型参数所**指定的类型**。
  - 对于返回类型参数表示对象的指令，也会执行一个类型检查，还会插入一个自动的向下转型，将对象从**边界类型向下转型**到类型参数所表示的类型。

- 任何需要在运行时知道确切类型的操作都无法运行（因为 `T` 的具体类型信息只存在于编译时期的检查，在运行时期信息已经丢失了）

  - 如`new T() `、`x instanceof T`

  - 虽然不能使用T进行类型检查，但是可以通过传入`class<T>`实现

  - ``` java
    class Building {}
    class House extends Building {}
    
    public class ClassTypeCapture <T> {
        Class <T> kind;
        public ClassTypeCapture(Class <T> kind) {
            this.kind = kind;
        }
        public boolean f(Object arg) {
            return kind.isInstance(arg);
        }
        public static void main(String [] args) {
            ClassTypeCapture <Building> ctt1 =
                new ClassTypeCapture <>(Building.class);
            System.out.println(ctt1.f(new Building()));
            System.out.println(ctt1.f(new House()));
            ClassTypeCapture <House> ctt2 =
                new ClassTypeCapture <>(House.class);
            System.out.println(ctt2.f(new Building()));
            System.out.println(ctt2.f(new House()));
        }
    }
    /* Output:
    true
    true
    false
    true
    */
    ```

### 生成泛型对象

- 由于不能直接通过T创建对象，需要使用特殊方法

- 法一，工厂模式

  - ``` java
    //生成者
    class ClassAsFactory <T> implements Supplier <T> {
      Class <T> kind;
      ClassAsFactory(Class <T> kind) {
        this.kind = kind;
      }
      @Override public T get() {
        try {
          return kind.getConstructor().newInstance();
        } catch(Exception e) {
          throw new RuntimeException(e);
        }
      }
    }
    ```

    - 这种方法要求必须存在无参构造器，像Integer就不行

  - ``` java
    //显式工厂，更加安全，避免出现缺少无参构造函数等运行时错误
    class Holder <T>{
        private T t;
        public void init(IFactory <T> factory){
            this.t = factory.create();  // 此处即为 new T()的工厂方法的实现
        }
    }
    interface IFactory <T>{  //接口也可以参数化
        T create();
    }
    class IntegerFactory implements IFactory <Integer>{
        public Integer create(){
            return new Integer(10);
        }
    }
    public class newTwithFactory{
        public static void main(String [] args){
            Holder <Integer> holder = new Holder <>();
            holder.init(new IntegerFactory());
        }
    }
    ```

  - 使用一个工厂方法，如 `IntegerFactory`，它**具体指明**了要创建的对象的类型（在这个例子中是 `Integer`）。

- 法二Class

  - 将`Class`对象作为类型标签来存储和使用类型信息。

  - 可以实现在运行中查询、使用对象类型。

  - ````java
    class Holder <T>{
        private T t;
        private Class <T> kind;
        public Holder(Class <T> kind){ this.kind = kind; }
        public void init(){
            try{
                this.t = kind.newInstance();
            }catch (Exception e){
                e.printStackTrace();
            }
        }
        public static void main(String [] args) {
            Holder <Integer> holder = new Holder <>(Integer.class);
            holder.init();
        }
    }
    ````

  - 只能使用无参构造

- ``` java
  class Fruit{}
  class Apple extends Fruit{}
  
  public class NonConvariantGeneric {
      List <Fruit> flist = new ArrayList <Apple>(); //编译错误
  }
  ```

  - Apple的`List`不是Fruit的`List`。Apple的`List`将持有Apple和Apple的子类型，Fruit的`List`将持有任何类型的`Fruit`。是的，这包括Apple，但是它**不是一个Apple**的`List`，**它仍然是Fruit**的`List`。Apple的`List`在**类型上**不等价于`Fruit`的List，**即使Apple是一种Fruit类型。**
  - Java泛型中规定，即使泛型类型具有继承关系，但是并不意味着该泛型类型的容器也具有继承关系。

- 泛型数组

  - 可以使用ArrayList直接创建`private class<T> array = new ArrayList<>();`

  - 先创建Object数组再进行转换

    - ``` java
      public GenericArray(int sz) {
          array = (T [])new Object [sz];
        }
      ```

    - 注意强制转型要使用`@SuppressWarnings("unchecked")`

  - 也可以使用Class创建出具有精确数据类型的数组，这样最终转化为T类型的数组更为安全

    - ``` java
      private T [] array;
        @SuppressWarnings("unchecked")
        public
        GenericArrayWithTypeToken(Class <T> type, int sz) {
          array = (T [])Array.newInstance(type, sz);
        }
      ```


### 协变与逆变

- **协变**（covariant），如果它保持了子类型序关系≦。该序关系是：子类型≦基类型。

- **逆变**（contravariant），如果它逆转了子类型序关系。

- **不变**（invariant），如果上述两种均不适用。

- 允许多参数，但不能重写一个函数

  - ``` java
    public class UseList <W,T>{
        void f(List <T> v){}
        void f(List <W> v){}//错误
    }
    ```

- 基类会劫持接口
  - 对于相同的泛型接口，子类不能实现与基类实现的接口的**泛型参数不同的**

#### 自限定类型

- CRG：奇异地规泛型

  - 泛型参数是无法直接继承的，但是可以继承一个在自身定义中用到了该泛型参数的类

  - ``` java
    class GenericType <T> {}
    
    public class CuriouslyRecurringGeneric
      extends GenericType <CuriouslyRecurringGeneric> {}
    ```

- 限制一个泛型类的类型参数，使其只能是该泛型类的**子类**。

  - ``` java
    public class BasicHolder <T> {
        T element;
        void set(T arg){this.element = arg;}
        T get(){return this.element;}
        void print(){
            System.out.println(element.getClass().getSimpleName());
        }
    }
    public class SubType extends BasicHolder <SubType> {
        public static void main(String [] args){
            SubType s1 = new SubType();
            SubType s3 = s1.get();
            s1.print();
            s3.print();
        }
    }
    ```

  - 即泛型类型**是自身**

  - 与普通继承的关系

    - 与普通继承相比，这里使用了泛型来确保类型安全，并在子类中**特化了基类的行为**。在普通的继承中，子类继承父类的属性和方法并可以重写它们。在这种情况下，子类不仅继承了基类的属性和方法，而且通过泛型参数为这些属性和方法**提供了一个具体的类型**（即其自身）。
    - 这意味着，对于 `SubType`，它继承了 `BasicHolder` 的所有方法，但这些方法现在具有固定的类型 - 也就是 `SubType`。所以，`SubType` 的一个实例可以保证 `get()` 方法返回的是 `SubType`，并且 `set()` 方法只接受 `SubType` 类型的参数。
    - 这提供了额外的**类型安全性**，并允许你在子类中为这些方法提供更具体的实现，同时不牺牲代码的复用性。

  - 泛型类强制实例时使用这种模式

    - ``` java
      //forcing the generic to be used as its own bound argument
      class SelfBounded <T extends SelfBounded<T> > {
          T element;
          SelfBounded <T> set(T arg){
              element = arg;
              return this;
          }
          T get(){return element;}
      }
      
      class A extends SelfBounded <A> {}
      class B extends SelfBounded <A> {} //ok 
      class E extends SelfBounded <D>{} //error
      
      public static void main(String [] args){
              A a = new A();
              a.set(new A());
              a.print();
              B b = new B(), a2 = new B();
              //b.set(b2); //Error
              //b.print();
          }
      ```

- 参数协变性
  - 方法参数类型会随着子类而变化
  - 允许子类方法比其重写的基类方法更具体的类型
  - 使用子限定类型就实现了将子类类型作为返回值

#### 通配符*

- 边界

  - 也可以对泛型进行一定限制

    - ``` java
      public class Computer <T extends Disk>{//必须是 Disk 或子类
      ```

  - 使用类型和接口限制

    - ``` java
      interface HasColor{ java.awt.Color getColor(); }
      
      class Colored <T extends HasColor>{...}
      
      class Dimension { public int x, y, z; }
      
      class ColoredDimension <T extends HasColor & Dimension>{...} //错误！
      class ColoredDimension <T extends Dimension & HasColor>{ //why？
          
      }
      ```

    - 类型在前接口在后，并且同样只能继承一个类

- 常用用于**方法参数**，**实现泛型类型的多态**，对于数组讨论集合自身的类型而不是所持有的元素类型

- 由于list不存在直接继承关系，可以使用通配符实现通用方法

  - ``` java
    public void print(List <?> animals) {
      animals.forEach(a -> System.out.println(((Animal) a).getFood()));
    }
    
    public static void main(String [] args) {
      Test test = new Test();
      List <Animal> animals = Arrays.asList(new Animal());
      List <Cat> cats = Arrays.asList(new Cat());
      test.print(animals);	// 正确执行
      test.print(cats);	// 正确执行
    }
    ```

  - ``` java
    class Fruit{}
    class Apple extends Fruit{}
    public class GenericsAndCovariance {
        public static void main(String [] args){
            //一个能放水果以及一切是水果派生类的盘子, 啥水果都能放的盘子
            //Plate <？ extends Fruit> 和 Plate <Apple> 最大的区别就是：
            //Plate <？ extends Fruit> 是 Plate <Fruit> 以及 Plate <Apple> 的基类。
            Plate <? extends Fruit> p = new Plate <Apple>(new Apple());
            // a list of any type that's inherited from Fruit
            List <? extends Fruit> flist = new ArrayList <Apple>();
        }
    }
    ```

- **协变**`Plate<？extends Fruit>`可以放**子类型**中任意的

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231012114311701.png" alt="image-20231012114311701" style="zoom:50%;" />

  - `List<?>`由于无法在编译期间确定泛型的实际类型，所以**没法**向`List<?>`中**添加**除了`null`外的任意类型元素。（即我不知道最高会是谁，因此我要是放入后得向下转型呢？但是读取可以，因为这总会是向上转型）

    - ``` java
      class Fruit{}
      class Apple extends Fruit{}
      
             Plate <? extends Fruit> p = new Plate <Apple>(new Apple());
              //不能存入任何元素
              p.set(new Fruit());    //Error
              p.set(new Apple());    //Error
              //读取出来的东西只能存放在 Fruit 或它的基类里。
              Fruit newFruit1 = p.get();
              Object newFruit2 = p.get();
              Apple newFruit3 = p.get();    //Error
      ```

    - 对于`Plate<? exyends fruit>`编译器并不知道具体会被初始化为什么类型，因此如果允许放入元素则可能造成不匹配（类型错误）

    - 而取出时会获得上界的类型，构成了多态

  - 对于非集合类型也会有限制，如`Tile<? extends Thing> tile;`

    - 读取，对于**返回T类型**的方法，可以安全的读取到Thing类型，因为这一定是一个子类
    - 赋值，对于有T类型参数的方法，通常无法调用，因为不知道确切的T泛型参数类型
    - 注意，这种参数并不是针对对tile变量的赋值和访问，而是针对对tile内部含有泛型参数/返回值的方法的调用

  - 有多个上界时使用`&`连接

- **逆变**`Plate<？super Fruit>`可以放基类中任意

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231012114813621.png" alt="image-20231012114813621" style="zoom:50%;" />

  - 我们在编译期只能知道`下界通配符`的下界是什么类型，所以在添加元素时，只能向其中**添加下界类型。**由于编译期无法知晓具体的实际类型，所以只能使用**`Object`来**接收获取的元素

    - ``` java
      class Fruit{}
      class Apple extends Fruit{}
      public class GenericsAndCovariance {
          public static void main(String [] args){
             Plate <? super Fruit> p = new Plate <Fruit>(new Fruit());
              //存入元素正常
              p.set(new Fruit());
              p.set(new Apple());
              //读取出来的东西只能存放在 Object 类里。
              Apple newFruit3 = p.get();    //Error
              Fruit newFruit1 = p.get();    //Error
              Object newFruit2 = p.get();
          }
      }
      ```

- **`List<?>`（无界通配符）**:

  - 这表示一个未知类型的列表。`?` 是一个通配符，代表任何类型。
  - 你可以从 `List<?>` 读取数据，读取的数据将被视为 `Object` 类型，但你不能向其中写入除 `null` 之外的任何数据，因为你不知道列表的确切类型。
  - 它在你不关心列表的具体类型，或者在使用只读操作时非常有用。
  
- **`List`（原生类型）**:

  - 这是一个原生类型的列表，没有泛型信息。在泛型被引入Java之前就存在。
  - 你可以向其中添加任何类型的对象，也可以从中读取数据，读取的数据被视为 `Object` 类型。
  - 它不安全，因为它不提供类型检查，可能导致运行时错误（例如，`ClassCastException`）。

- **`List<Object>`**:

  - 这表示一个可以包含任何类型对象的列表。
  - 与 `List<?>` 不同，你可以安全地向 `List<Object>` 添加任何类型的对象（除了基本类型，它们需要被装箱）。
  - 这是一个明确声明你可以存储任何类型对象的列表，提供了类型安全性（即使是 `Object` 级别的）。

#### 混型

- 混合多个类的能力，生成一个可以代表混型中全部类型的类

- 使用接口进行混合

  - 因为接口可以多继承，并且在类内保存所有接口的实例

  - ``` java
    class Mixin extends BasicImp
    implements TimeStamped, SerialNumbered {
      private TimeStamped timeStamp = new TimeStampedImp();
      private SerialNumbered serialNumber =
        new SerialNumberedImp();
      @Override public long getStamp() {
        return timeStamp.getStamp();
      }
      @Override public long getSerialNumber() {
        return serialNumber.getSerialNumber();
      }
    }
    ```

- 动态代理

  - ``` java
    import java.lang.reflect.*;
    // Step 1: Define interfaces
    interface Interface1 {
        void doSomething();
    }
    interface Interface2 {
        void doSomethingElse();
    }
    // Step 2: Create implementations
    class Implementation1 implements Interface1 {
        public void doSomething() {
            System.out.println("Implementation1 doing something");
        }
    }
    class Implementation2 implements Interface2 {
        public void doSomethingElse() {
            System.out.println("Implementation2 doing something else");
        }
    }
    // Step 3: Create dynamic proxy
    class MixinProxy implements InvocationHandler {
        private final Object [] delegates;
    
        public MixinProxy(Object... delegates) {
            this.delegates = delegates;
        }
    
        public Object invoke(Object proxy, Method method, Object [] args) throws Throwable {
            for (Object delegate : delegates) {
                if (method.isInstance(delegate)) {
                    return method.invoke(delegate, args);
                }
            }
            throw new UnsupportedOperationException("Method not supported: " + method);
        }
        //帮助创建动态代理
        @SuppressWarnings("unchecked")
        public static <T> T createMixinProxy(Class <T> interfaceClass, Object... delegates) {
            //取出传入对象的类型
            Class <?> [] interfaces = new Class <?> [delegates.length];
            //存储接口
            for (int i = 0; i < delegates.length; i++) {
                interfaces [i] = delegates [i].getClass().getInterfaces()[0];
            }
            return (T) Proxy.newProxyInstance(
                    interfaceClass.getClassLoader(),
                    interfaces,
                    new MixinProxy(delegates)
            );
        }
    }
    // Using the mixin
    public class MixinDemo {
        public static void main(String [] args) {
            Object mixin = MixinProxy.createMixinProxy(Object.class, new Implementation1(), new Implementation2());
            //还需要手动转换类型
            Interface1 if1 = (Interface1) mixin;
            Interface2 if2 = (Interface2) mixin;
            if1.doSomething();
            if2.doSomethingElse();
        }
    }
    ```

#### 潜在类型机制

> 一个东西长得像鸭子，行为像鸭子，那他就是鸭子

- 不一定是同一类型，有正确方法就可以用

  - ``` c++
    class Dog {
    public:
      void speak() { cout << " Arf!" << endl; }
      void sit() { cout << " Sitting " << endl; }
      void reproduce() {}
    };
    
    class Robot {
    public:
      void speak() { cout << " Click!" << endl; }
      void sit() { cout << " Clank!" << endl; }
      void oilChange() {}
    };
    
    template <class T> void perform(T anything) {
      anything.speak();
      anything.sit();
    }
    ```

- 在Java中没法实现，因此叫辅助潜在类型机制

- 使用方法引用：不是传入对象，而是传入方法实现调用

  - ``` java
    class PerformingDogA extends Dog {
      public void speak() { System.out.println("Woof!"); }
      public void sit() { System.out.println("Sitting"); }
      public void reproduce() {}
    }
    
    class RobotA {
      public void speak() { System.out.println("Click!"); }
      public void sit() { System.out.println("Clank!"); }
      public void oilChange() {}
    }
    
    class CommunicateA {
      public static <P> void perform(P performer,
        Consumer <P> action1, Consumer <P> action2) {
        action1.accept(performer);
        action2.accept(performer);
      }
    }
    
    public class DogsAndRobotMethodReferences {
      public static void main(String [] args) {
        CommunicateA.perform(new PerformingDogA(),
          PerformingDogA:: speak, PerformingDogA:: sit);
        CommunicateA.perform(new RobotA(),
          RobotA:: speak, RobotA:: sit);
        CommunicateA.perform(new Mime(),
          Mime:: walkAgainstTheWind,
          Mime:: pushInvisibleWalls);
      }
    }
    ```

## 自省与反射

- 摆脱只能在编译时执行**面向对象类型的操作**的限制

- **自省**是指程序在运行时**检查对象类型或属性的能力**。它通常用于获取关于对象的信息，如类名、方法名、属性等，而**不改变对象的行为。**

- **反射**是指程序在运行时创建、检查和**修改其自身行为的能力**。与自省相比，反射不仅能获取对象的信息，还能**在运行时改变对象的属性和行为。**

- 核心功能

  - 在运行时**判断**任意一个对象**所属的类**；

  - 在运行时**构造**任意一个类的**对象**；

  - 在运行时**判断**任意一个类所具有的**成员变量和方法**；

  - 在运行时**调用**任意一个对象的**方法**；

- 向上转型可以自动发生，但是向下转型必须显示指出，并且如果这个对象并转型到目标并不是向下转型（不是其父类），会抛出ClassCastException

  - 通过反射可以获取实际类型并且进行安全的向下转型

  - ``` java
    Animal animal = getAnimal(); // getAnimal()返回一个 Animal 对象
    if (animal instanceof Dog) {
        Dog dog = (Dog) animal;
        // 在这里，你可以安全地调用 Dog 类的方法
    }
    ```

### Class对象

- Class对象包含了与类相关的信息，Class对象被用来创建常规对象，Java使用Class对象执行反射

- 每个类都有一个Class对象，存储在同名的class文件中

- 类在首次使用时才会被动态加载到JVM，类加载器检查是否加载了类型的Class对象，如果没有就定位到对应的class文件并校验后加载，Class对象加载到内存后会用于创建类的所有对象

  - 类第一次被加载时会**调用类的静态初始快**

- 获得Class对象的引用`Class.forName("classname");`

  - 需要使用包含包名称的完全限定类型
  - 找不到则抛出ClassNotFoundException
  - 如果有对应类型的对象，可以通过对象获取`xx.getClass()`

- 使用类字面常量获取Class

  - `ClassName.class'`编译时检查们不用放在try
    - 包装类使用`TYPE`关键字
  - 不一定会立即导致初始化，等到首次使用静态方法再初始化，对于static final的编译时常量不会导致初始化
  - 会生成确切（泛型）类型的Class，而不是Object

- 常用方法

  - `getName()`获取完整类名（含包路径），外部类内部类用$分隔

    - `getCanonicalName()`获取规范名，wai不累内部类用.分隔，可能返回null（比如匿名内部类）

  - `isInterface()`

  - `getSimpleName()`

  - `getInterfaces()`返回Class数组，即所有实现的接口类型

  - `getSuperClass()`查询直接基类

  - `Method[] getDeclaredMethods()`返回当前 `Class` 对象所表示的类或接口的所有声明的方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

  - `Annotation[] getDeclaredAnnotations() `获取全部注解

  - `newInstance()`得到一个Object类型的引用，类型必须要有无参构造器（非public类要手动提供一个public的无参构造）

    - 新的方法

    - ``` java
      //无参构造
      try {
          return type.getConstructor().newInstance();
      } catch(Exception e) {
          throw new RuntimeException(e);
      }
      //有参构造
      Class <?> clazz = MyClass.class;
      Constructor <?> constructor = clazz.getConstructor(String.class, int.class);
      MyClass myObject = (MyClass) constructor.newInstance("example", 123);
      ```

- 泛型类的引用

  - `Class<Integer>`只接受特定类型的Class

  - 通配`Class<?>`（实际上<?>可以省略）

    - `Class<? extends Number>`可以存储整数/浮点数

  - ``` java
    Class <FancyToy> ftc = FancyToy.class;
        // Produces exact type:
        FancyToy fancyToy =
          ftc.getConstructor().newInstance();
        Class <? super FancyToy> up = ftc.getSuperclass();
        // 虽然知道 Toy 是基类，但编译不通过，只能是 FancyToy 的某个基类
        // Class <Toy> up2 = ftc.getSuperclass();
    	//不确定具体类型，使用 Object 引用
        Object obj = up.getConstructor().newInstance();
    ```

- cast方法

  - Class引用类型转换

  - 用于将对象转换为由 `Class` 实例表示的类或接口

  - 这个方法在运行时动态地执行类型转换，与强制类型转换类似，但更加灵活，因为它允许在**编译时不知道具体类**的情况下进行转换。

  - ``` java
    Class <String> clazz = String.class;
    Object obj = "Hello";
    String str = clazz.cast(obj);
    //str = (String)obj
    ```

- 类方法提取器

  - 可以查看一个类（包含其基类在内）的全部可用方法

  - ``` java
    //全部方法
    Method [] methods = c.getMethods();
    //全部构造方法
    Constructor [] ctors = c.getConstructors();
    ```

- 方法Method
  - `getName()`: 返回方法的名称。
  - `getReturnType()`: 返回方法的返回类型。
  - `getParameterTypes()`: 返回方法参数的类型数组。
  - `getModifiers()`: 返回方法的修饰符，如 `public`, `private` 等。
  - `getExceptionTypes()`: 返回方法声明抛出的异常类型数组。
    - 动态调用方法`method.invoke(Object obj, Object... args)`: 允许你动态地调用方法。`obj` 是要调用其方法的对象，`args` 是方法调用时传递的参数。
  - `getAnnotation(Class<T> annotationClass)`: 返回该方法上指定类型的注解。
  - `getAnnotations()`: 返回该方法上所有注解的数组。
  - `getGenericReturnType()`: 返回方法的泛型返回类型。
  - `getGenericParameterTypes()`: 返回方法的泛型参数类型数组。
- 字段Field
  - 获取类或接口的全部字段`getDeclaredFields()`
  - `getName()`：返回字段的名称。
  - `getType()`：返回一个`Class`对象，该对象表示字段的类型。
  - `get(Object obj)`：返回指定对象上此字段的值。
  - `set(Object obj, Object value)`：将指定对象上此字段的值设置为指定的新值。
  - `getModifiers()`：返回此字段的Java语言修饰符，这可以用来检查字段是否为`public`，`private`，`protected`，`static`等。
  - `getDeclaredAnnotations()`：返回此元素上直接存在的所有注解。

### 转型前检查

- 类型检查`x instanceof Dog`返回布尔值，是否是特定类型的对象
  - 在向下转型之前进行检查，防止遇到ClassCastException
  - 必须使用命名类型进行比较，不能通过Class，也就是说不饿能创建一个数组自动化执行`instanceiof`
- 动态isInstance
  - `Class.isInstance()`
  - 可以使用Class数组方便的使用`Class对象.isInstance(待判断类型的对象)`
- instanceof和isInstance的比较是等价的
  - 行同类型/基类都满足比较的条件
  - **但是使用==或equal则表示要求**类型完全相同
- isAssignableFrom
  - 判断一个类是否相同或是另一个类的超类或接口。
  - `parentClass.isAssignableFrom(childClass);`

### 动态代理

- 想将额外的操作从实际对象分离，可以通过代理去调用对象，通过是否使用代理对对象进行操作，决定是否要进行一些额外操作

- 动态代理允许在运行时创建一个符合某些给定接口的代理对象，此对象可以在调用实际方法前后执行特定操作。

- ``` java
  interface Interface {
    void doSomething();
    void somethingElse(String arg);
  }
  
  class RealObject implements Interface {
    @Override public void doSomething() {
      System.out.println("doSomething");
    }
    @Override public void somethingElse(String arg) {
      System.out.println("somethingElse " + arg);
    }
  }
  
  class DynamicProxyHandler implements InvocationHandler {
    private Object proxied;
    DynamicProxyHandler(Object proxied) {
      this.proxied = proxied;
    }
      //任何通过代理对象发起的方法调用都会被转发到这个接口的 invoke 方法。
    @Override public Object
    invoke(Object proxy, Method method, Object [] args)
    throws Throwable {
      System.out.println(
        "**** proxy: " + proxy.getClass() +
        ", method: " + method + ", args: " + args);
      if(args != null)
        for(Object arg : args)
          System.out.println("  " + arg);
      return method.invoke(proxied, args);
    }
  }
  
  class SimpleDynamicProxy {
    public static void consumer(Interface iface) {
      iface.doSomething();
      iface.somethingElse("bonobo");
    }
    public static void main(String [] args) {
      RealObject real = new RealObject();
      consumer(real);
      // 创建动态代理（在运行时动态创建一个实现了指定接口的代理对象）
      Interface proxy = (Interface)Proxy.newProxyInstance(
        Interface.class.getClassLoader(),
        new Class []{ Interface.class },//会被代理的接口（方法），可以有多个
        new DynamicProxyHandler(real));
        //得到了一个代理对象
      consumer(proxy);
    }
  }
  ```

  - `Proxy.newProxyInstance` 需要三个参数：类加载器、一组接口以及一个 `InvocationHandler` 实例。          

### 标签接口

- 标签接口是没有定义任何方法的接口。标签接口的主要作用是**为实现该接口的类提供一个特定的标识**。这个标识允许类在运行时表现出一些特殊的行为。

- ``` java
  class NullRobotProxyHandler
  implements InvocationHandler {
    private String nullName;
    private Robot proxied = new NRobot();
    NullRobotProxyHandler(Class <? extends Robot> type) {
        //区分不同 Robot 子类的 Null 对象
      nullName = type.getSimpleName() + " NullRobot";
    }
      //NULL 就是一个标签接口
    private class NRobot implements Null, Robot {
      @Override
      public String name() { return nullName; }
      @Override
      public String model() { return nullName; }
      @Override public List <Operation> operations() {
        return Collections.emptyList();
      }
    }
      //传递操作
    @Override public Object
    invoke(Object proxy, Method method, Object [] args)
    throws Throwable {
      return method.invoke(proxied, args);
    }
  }
  
  public class NullRobot {
      //通过代理创建具有空标签的对象
    public static Robot
    newNullRobot(Class <? extends Robot> type) {
      return (Robot)Proxy.newProxyInstance(
        NullRobot.class.getClassLoader(),
        new Class []{ Null.class, Robot.class },
        new NullRobotProxyHandler(type));
    }
    public static void main(String [] args) {
      Stream.of(
        new SnowRobot("SnowBee"),
        newNullRobot(SnowRobot.class)
      ).forEach(Robot:: test);
    }
  }
  ```

  - 使用代理的提供一种**通用的方式**来创建任何类型的`Robot`的空对象，而不需要为每个`Robot`子类都创建一个特定的空对象类。
  - 通过使用代理，你可以为任何`Robot`子类创建一个空对象，而不需要写任何额外的代码。这是因为代理对象的所有方法调用都会被转发给`NullRobotProxyHandler`对象，然后由这个对象的`invoke`方法处理。这个方法将方法调用转发给`NRobot`对象，这个对象的所有方法都返回一个无操作的结果。

### 接口和类型信息

- 把一个实现接口的类型赋值给接口类型，可以隐藏许多信息，只保留接口规定的方法，但是通过反射可以发现这一点并进行强制转换，从而破坏限制

  - 将实现接口的类声明为包作用域可以避免，因为包外根本没有这个类。但是虽然不能转型，如果知道方法的名字，仍然可以访问！

  - ``` java
    callHiddenMethod(Object a, String methodName)
      throws Exception {
        //获取方法
        Method g =      a.getClass().getDeclaredMethod(methodName);
        //设置为允许访问
        g.setAccessible(true);
        g.invoke(a);
      }
    ```

  - 无论是使用private方法还是匿名内部类都无法阻止

## 异常

- “**异常**”这个词有“我对此感到意外”的意思。问题出现了，**也许不清楚该如何处理**，但知道**不应该置之不理**，要停下来，看看是不是有别人或在别的地方，能够处理这个问题。只是在当前的环境中还没有足够的信息来解决这个问题，所以就把这个问题**提交**到一个**更高级别的环境中**，在那里将作出正确的决定。

- 一个方法后跟`throws`关键字，它表示这个方法可能会抛出指定的一个或多个异常。当调用这样的方法时，调用者要么需要使用`try-catch`块来**捕获和处理这些异常**，要么也需要在其方法签名中声明`throws`，从而**传递异常给其上级调用者**。

- ``` java
  public class Calculator {
      public int div(int a, int b) throws Exception {
          if (b == 0) {
              throw new Exception("divided by zero");
          }
          return a / b;
      }
      public static void main(String [] args) {
          try {
              System.out.println(new Calculator().div(Integer.parseInt(args [0]), 
                                                      Integer.parseInt(args [1])));
          } catch (Exception e) {
              System.out.println("exception!");
          }
      }
  }
  ```

### Java异常机制

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231019105850829.png" alt="image-20231019105850829" style="zoom:50%;" />

  - throwable包含全部的异常可错误

  - Error是严重的问题（系统级错误），普通的程序不应该捕获处理

  - **受检异常**：由`Exception`类及其子类（但不包括`RuntimeException`的子类）表示的。当一个方法或构造函数可能会抛出这类异常时，**必须在方法或构造函数的签名中使用`throws`子句来声明它**。调用该方法或构造函数的代码**必须处理或再次声明该异常。**

    - ``` java
      //会读取错误时自动抛出异常
      private static void checkedExceptionWithThrows() throws FileNotFoundException {
          File file = new File("not_existing_file.txt");
          FileInputStream stream = new FileInputStream(file);
      }
      ```

  - **未检异常**：也称为运行时异常，它们是`RuntimeException`及其子类表示的。这类异常不需要强制声明或捕获，因为它们通常代表**编程错误**，例如空指针或数组越界。

- 异常链

  - 抛出一个新的异常时可以仍然保留原始异常信息
  - Throwable子类的构造器都支持传入一个原始异常`Throwable(String message, Throwable cause)`
    - 此外只有Error、Exception、RuntimeException提供了此种构造，其它异常类型使用initCause
    - `e.initCause(cause)`

- 异常的约束

  - 重写方法时只能抛出**基类版本**中说明的异常。也可以选择不抛出
    - 当继承的基类与子类实现的接口冲突时以基类为准
  - 可以为构造器添加新异常，但是必须包含基类构造器的异常，子类也不应该捕获基类构造器的异常
  - 即对于基类和子类，异常可以缩小但是不能扩大

- 构造器在编写构造器时要注意在发生异常时保证资源的正常释放

  - 并不是在finally中释放就一定可以解决，因为此时可能在创建之前就中断了（如读取文件失败就不能关闭文件）

    - ``` java
      public InputFile(String fname) throws Exception {
          try {
              in = new BufferedReader(new FileReader(fname));
              // Other code that might throw exceptions
          } catch(FileNotFoundException e) {//文件根本没打开，不用关
              System.out.println("Could not open " + fname);
              // Wasn't open, so don't close it
              throw e;
          } catch(Exception e) {
              // All other exceptions must close it
              try {
                  in.close();
              } catch(IOException e2) {
                  System.out.println("in.close() unsuccessful");
              }
              throw e; // Rethrow
          } finally {
          }
      }
      ```

  - 更好的嵌套格式

    - ``` java
      public static void main(String [] args) {
          try {
              InputFile in = new InputFile("Cleanup.java");
              try {
                  String s;
                  int i = 1;
                  while((s = in.getLine()) != null)
                      ; // Perform line-by-line processing here...
              } catch(Exception e) {
                  System.out.println("Caught Exception in main");
                  e.printStackTrace(System.out);
              } finally {
                  in.dispose();
              }
              //打开文件发生异常，不用关闭
          } catch(Exception e) {
              System.out.println(
                  "InputFile construction failed");
          }
      }
      ```

  - 一般来说，在创建了一个需要清理的对象之后应该直接跟一个try-finally用于在发生错误时自动进行清理。如果构造也会失败（像文件那样），则需要双层嵌套

    - ``` java
      .. = new ...
      try{
          
      }
      finally{
          ..close
      }
      ```

- try-with-resources

  - 对于一个类，比如要在生命周期持续对文件读取，则会导致每一步都可能引发异常，这很危险和麻烦

  - 一种解决办法：尽可能一次性实现文件的打开读取和关闭  ，也看了一使用文件创建流，后续的操作通过流来实现

  - 在try后面可以跟一个()，即资源说明头， 

    - 用于那些实现了 `java.lang.AutoCloseable` 或 `java.io.Closeable` 接口的资源.比如Stream、文件读取等
    - 在代码块结束时**自动释放资源**

  - ``` java
    try (BufferedReader reader = new BufferedReader(new FileReader("example.txt"))) {
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {//可选的异常捕获
        e.printStackTrace();
    }
    ```

  - 即提供了一种类似文件作用域的机制，在范围内使用文件，之后会被自动关闭

- 异常匹配

  - 父类catch可以捕获子类的异常类型，即可以匹配异常的基类

### 抛出异常

- 使用new在堆上创建异常
- 所有异常都可以接受一个String参数
  - `throw new NullPointerException("t=null");`
  - string可以在之后提取出来用于获取关于异常的信息
- 输出异常跟踪信息
  - `e.printStackTrace(System.out);`
- 如果要抛出异常就要在方法函数体之前说明
  - （除了从RunTimeException继承来的异常，这些异常可以随处抛出）
  - `void f() throws TooBig, TooSmall{}`
  - 如果声明了会抛出某些异常，则编译器会强制用户对异常进行处理
    - 这种在编译器时被检查并强制实施的异常叫做检查型异常
  - 如果方法中声明throws某种异常，那么如果在执行方法时方法内某处出现了该种异常并且没有被处理，则会自动向上继续抛出（而不需要手动重新抛出）

#### 自定义的异常类型

- 从异常类Exception创建`class SimpleException extends Exception {}`

  - 带有完整构造器的版本

  - ```
    class SimpleException extends Exception {
    	MyExtion(){}
    	MyException(String msg){super(msg);}
    }
    ```

### 捕获异常

- 处理异常

  - ``` java
    try {
        // The guarded region: Dangerous activities
    } catch(A a1) {
        // Handler for situation A
    } finally {//无论是否抛出异常总是会被执行
        // Activities that happen every time
    }
    ```

- finaly的用途

  - 无论是否发生异常，保证最终状态结果的一致性
  - 也可以用于非异常的情况，无论是使用了break、continue、return，finally的内容也总是会被使用
  
- 捕捉任何异常：使用基类`Exception`

  - 应该放在最后

- 获取异常的信息

  - `getMessage() getLocalizedMessage()`
  - ` printStackTrace()`
    - 以数组的形式获取`getStackTrace()`

  - 获取包信息`getName()`
  - 获取类名`getSimpleName()`
  - 可以重写并使用tostring定制输出

- 多重捕获

  - ``` java
    public class SameHandler {
        void x() throws Except1, Except2, Except3, Except4 {}
        void process() {}
        void f() {
            try {
                x();
            } catch(Except1 e) {
                process();
            } catch(Except2 e) {
                process();
            } catch(Except3 e) {
                process();
            } catch(Except4 e) {
                process();
            }
        }
    }
    ```

  - ``` java
    try {
        x();
    } catch(Except1 | Except2 | Except3 | Except4 e) {
        process();
    }
    ```

    - 可以用`|`表示or

- 重新抛出

  - 重抛异常会把异常抛给上一级环境中的异常处理程序，同一个 try 块的后续 catch 子句将被忽略。

  - ``` java
    catch(Exception e) {
        System.out.println("An exception was thrown");
        throw e;
    }
    ```

  - 要注意的是重新抛出不会对异常栈进行修改，也就是说每一层得到的栈信息是一致的，缺失传递过程

    - 使用`throw e.fillInStackTrace();`

- 异常匹配

  - 异常匹配机制按照`catch`子句的顺序进行。当异常发生时，会查找第一个与之匹配的`catch`子句。一旦找到匹配的`catch`子句，将执行该子句中的代码，然后继续执行`try-catch`块后面的代码，不再检查其它的`catch`子句。

  - 同样，对于异常的子类也被会匹配

  - ``` java
    class SuperException extends Exception { }
    class SubException extends SuperException { }
    class BadCatch {
      public void goodTry() {
        try { 
          throw new SubException();
        } catch (SuperException superRef) { ...
        } catch (SubException subRef) {
          ...// never be reached
        } // an INVALID catch ordering
      }
    }
    ```

- RuntimeException
  - 不需要声明及手动抛出
  - 出现时会自动抛出并向上冒泡，如果没有被捕获就一直向上冒泡
  - 如果到达main仍然没有被处理则会导致程序终止并打印异常堆栈信息

## 代码校验

### 单元测试

- 测试覆盖率

#### Junit

- 在项目引入

  - ``` java
    //Maven
    <dependency>
        <groupId> junit </groupId>
        <artifactId> junit </artifactId>
        <version> 5.7.0 </version>
        <scope> test </scope>
    </dependency>
    ```

  - 使用（Maven Surefire 插件）执行`mvn test`

- 测试用例

  - `@Test`标记测试方法(会被自动执行)

  - 使用断言（如 `assertEquals`）来验证预期结果与实际结果是否一致。

  - ``` java
    public class Calculator {
        public int add(int a, int b) {
            return a + b;
        }
    }
    
    import static org.junit.Assert.assertEquals;
    import org.junit.Test;
    
    public class CalculatorTest {
    
        @Test
        public void testAdd() {
            Calculator calculator = new Calculator();
            int result = calculator.add(5, 3);
            assertEquals(8, result);
        }
    }
    ```

- 生命周期注解

  - `@BeforeAll`、`@AQfterAll`
  - `@BeforeEach`、`@AfterEach`

### 前置条件

- 断言
  - `assert boolean-expression[: information-expression]`
  - 可选字符串用于输出显示
  - 断言**默认不会生效**，需要在运行时添加`-ea`参数
    - 或者`ClassLoader.getSystemClassLoader().setDefaultAssertionDtstatus(true);`

- Google Guava实现的断言

  - 提供始终启用的断言

  - ``` java
    import com.google.common.base.*;
    import static com.google.common.base.Verify.*;
    verify(1 + 2 == 4);
    verify(1 + 2 == 4, "Bad math");
    verify(1 + 2 == 4, "Bad math: %s", "not 4");
    verifyNotNull(s);
    s = verifyNotNull(s);
    ```

#### 契约式设计Dbc

- 前置条件
  - 确保调用方法的代码符合要求
- 后置条件
  - return语句之前，验证计算结果
- 不变项
  - 对象在方法调用之间保持不变的状态

### 基准测试

- 计算一段代码的运行时间

#### 使用JMH微基准测试工具

- maven导入

  - ``` xml
    <dependencies>
      <dependency>
        <groupId> org.openjdk.jmh </groupId>
        <artifactId> jmh-core </artifactId>
        <version> 1.33 </version> <!-- Use the latest version -->
      </dependency>
      <dependency>
        <groupId> org.openjdk.jmh </groupId>
        <artifactId> jmh-generator-annprocess </artifactId>
        <version> 1.33 </version> <!-- Use the latest version -->
      </dependency>
    </dependencies>
    
    ```

- 注解

  - ``` java
    //测试方法
    @Benchmark
    public void myTest() {
        // benchmarked code here
    }
    //测试前的初始化
    @Setup
    public void setup(){
        
    }
    //测试之后的清理
    @TearDown
    //基准测试的独立运行次数
    @Fork(1)
    //测试的线程数
    @Threads(Threads.MAX)
    //预热的迭代次数和时间
    @Warmup
    //实际测试的迭代次数和时间
    @Measurement
    //多组测试（值必须是字符串）
    @Param({
        "1",
        "10"
    })
    int size;
    ```

- 预设

  - ``` java
    @State(Scope.Thread)
    @BenchmarkMode(Mode.AverageTime)
    @OutputTimeUnit(TimeUnit.MICROSECONDS)
    @Warmup(iterations = 5)
    @Measurement(iterations = 5)
    @Fork(1)
    ```

### 补充

- 样式检查（编码风格）：Chgeckstyle
- 静态错误分析：Findbugs

## 并发编程

- 构造器不是线程安全的

- ``` java
  public class StaticIDField implements HasID {
    private static int counter = 0;
    private int id = counter++;
    @Override public int getID() { return id; }
  }
  //线程安全的版本
  public class GuardedIDField implements HasID {
    private static AtomicInteger counter =
      new AtomicInteger();
    private int id = counter.getAndIncrement();
    @Override public int getID() { return id; }
    public static void main(String [] args) {
      IDChecker.test(GuardedIDField:: new);
    }
  }
  ```

- 不能叫构造方法设置为同步（串行）可以通过工厂方法间接实现

### 基本概念

- 并发：同时**处理多个任务，不必等待**一个任务完成就开始处理其他任务。解决阻塞问题，如IO，一个任务在等待输入时会阻塞，类似的场景为**IO密集型**问题。并发是一系列聚焦于如何**减少等待**并提升性能的技术。
  - 对于单处理器并发也是有意义的，比如处理阻塞问题，但这也是唯一的意义了
  - 应用：事件驱动编程，如建立高响应的应用程序 
- 并行：**同时在多处执行**多个任务，解决**计算密集型**问题，将任务分为多个部分，并在多个处理器上执行，提高程序的运行速度。

#### 线程

- 并发将程序分割成多个独立运行的任务，每个人物就是一个线程。
- Java使用Thread管理线程，创建Thread时JVM会在专门保留的内存区域中分配一大块空间。包含许多内容
  - 程序计数器，指示要执行的下一条字节码
  - 支持Java代码执行的栈，包含线程到达当前执行结点**前所调用过的方法的相关信息**以及正在执行的方法的本地变量（基本类型和对象引用）
    - 堆由所有线程共享
  - 本地代码栈
  - 本地线程变量存储
  - 用于控制线程的状态维护变
- 获取机器上的处理器数量`Runtime.getRuntime().availableProcessors();`
  - 通常来说线程的最佳数目就是可用处理器的数量
  - 由于超线程只是会大幅加快上下文切换的速度，而并不会增加实际的计算吞吐量。因此设置计算密集型程序时不应该考虑超线程
  - 大部分情况由JVM决定即可

##### 死锁

- 循环等待造成无限循环
- 死锁发生的条件
  - 互斥：任务使用的至少一项资源不是共享的（即同一时间只能被一个对象使用，其他对象必须等待）
  - 至少一个任务必须**持有**一项资源，并且**等待**正在被另一个任务持有的资源。
  - 不能从一个任务中夺走一项资源。
  - 会发生循环等待。

##### 线程池

- 线程池类型

  - FixedThreadPool：创建一个**固定大小**的线程池。每提交一个任务就创建一个线程，直到线程达到线程池的最大大小。

    - `ExecutorService executor = Executors.newFixedThreadPool(int nThreads);`
    - 当所有线程都在忙时，新提交的任务不会立即执行，而是会放入线程池的工作队列中等待。

  - CachedThreadPool：创建一个可缓存的线程池。如果线程池长度超过处理需求，可灵活回收空闲线程，若无可回收，则新建线程。（无数目限制，除非超过内存）

    - `ExecutorService executor = Executors.newCachedThreadPool();`

  - SingleThreadExecutor：创建一个单线程的Executor。如果这个线程因异常而结束，会创建一个新线程来继续执行后续的任务。

    - `ExecutorService executor = Executors.newSingleThreadExecutor(); `

  - ScheduledThreadPool：创建一个大小无限制的线程池。支持定时以及周期性执行任务的需求。

    - ``` java
      ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(int corePoolSize);
      scheduler.schedule(Callable <V> callable, long delay, TimeUnit unit);
      ```

  - WorkStealingPool：基于工作窃取的线程池。适用于大量的短任务，使用多个队列减少竞争。(使用双端队列为每个线程维护任务，如果一个线程没有任务会从别的线程“窃取”任务)

    - `ExecutorService executor = Executors.newWorkStealingPool(int parallelism);`

- 基本使用方法

  - `execute(Runnable command)`: 提交不需要返回值的任务。
  - `submit(Runnable task)`: 提交需要返回值的任务，并返回 `Future` 对象。
  - `submit(Callable<T> task)`: 提交有返回值的任务，返回 `Future<T>`。
  - `executor.shutdown();`关闭线程池，仍然会执行完已经提交的任务，但是不接受新任务。

##### 异常

- 无法捕获已逃离线程的异常，一旦异常逃逸到任务的run()方法之外便会扩散到控制台，在execute语句包上try-catch是没用的

- 需要创建一个新的TreadFactory类型，为创建的Tread对象添加Thread.UncaughtExceptionHandler用于添加异常处理程序

  - ``` java
    //自定义异常处理
    class MyUncaughtExceptionHandler implements
        Thread.UncaughtExceptionHandler {
        @Override
        public void uncaughtException(Thread t, Throwable e) {
            System.out.println("caught " + e);
        }
    }
    //绑定异常处理对象的工厂方法
    class HandlerThreadFactory implements ThreadFactory {
        @Override public Thread newThread(Runnable r) {
            System.out.println(this + " creating new Thread");
            Thread t = new Thread(r);
            System.out.println("created " + t);
            t.setUncaughtExceptionHandler(
                new MyUncaughtExceptionHandler());
            System.out.println(
                "eh = " + t.getUncaughtExceptionHandler());
            return t;
        }
    }
    //创建线程池时使用自定义的工厂方法
    ExecutorService exec = Executors.newCachedThreadPool(new HandlerThreadFactory());
    //设置全局
    Thread.setDefaultUncaughtExceptionHandler(new MyUncaughtExceptionHandler());
    ```

#### 共享资源

- 资源竞争：正在运行的任务不止有一个时，任何任务都可能同时对一个共享资源进行读或写操作
- 守护进程
  - 一个定时运行的进程或线程，它监**控应用程序的执行时间**，如果某个任务或整个应用程序运行时间超过预定阈值，它将执行中止操作。、
  - 放置卡死
- 解决资源竞争
  - 第一个访问资源的任务对资源上锁，其它任务将无法使用资源直到锁被解除，使用资源的新的任务会再次对资源上锁。即共享资源的访问操作**串行化**，被称为**互斥锁**。

- synchronized
  - 一个任务想要执行由synchronized保护的代码断时，编译器会检查所是否可用，如果可用，该任务便会获得锁，执行代码然后释放锁。
  - 声明在普通方法上时：锁定的是包含该方法的对象实例
    - 声明在静态方法上：锁定的是该类的Class对象。
    - `public synchronized void synchronizedMethod()`
  - 如果在对一个**接下来**会被另一个线程**读取**的变量进行写操作或者操作一个可能**刚被**另一个线程**写完**操作的变量，就必须使用同步
  - 比如一个变量有自增以及获取值得方法，那这两个方法都应该被synchronized修饰

#### 临界区

- 指向防止多个线程同时访问方法中的部分代码，而不是整个方法，要隔离的代码区域就是**临界区**。

- ``` java
   synchronized(this) {
          // 同步代码块
  }//参数表示要锁定的对象
  ```

  - 必须先获得被锁定对象的锁才能进入代码块

- 使用临界区主要是为了提升性能，即不锁定不需要锁定但是耗时的部分

- 在其它对象上进行同步

  - 通过传入其他对象作为参数，可以实现在其他对象而不是对象自身上操作锁

- 使用显示lock对象

  - 显式创建加锁解锁
    - `Lock lock = new ReentrantLock();`
    - `lock.lock();`
    - `lock.unlock();`
  - lock语句应该与try-finally配合确保锁一定会被释放

  - ``` java
    //尝试获取锁
    boolean captured = lock.tryLock();
    try {
        System.out.println("tryLock(): " + captured);
    } finally {
        if(captured)
            lock.unlock();
    }
    //有最大时间限制
    try {
        captured = lock.tryLock(2, TimeUnit.SECONDS);
    } catch(InterruptedException e) {
        throw new RuntimeException(e);
    }
    try {
        System.out.println(
            "tryLock(2, TimeUnit.SECONDS): " + captured);
    } finally {
        if(captured)
            lock.unlock();
    }
    ```

  - 更加灵活，可以实现不同种类的锁

### 直接操作线程（Java高级程序设计课程内容）

#### 线程的创建与运行

- 使用线程运行对象的函数式接口

  - ```java
    class LiftOff implements Runnable {
        protected int countDown = 10; // Default
        private static int taskCount = 0;
        private final int id = taskCount++;
        public LiftOff(int countDown) { this.countDown = countDown; }
        public String status() {
            return "#" + id + "(" + (countDown > 0 ? countDown : "Liftoff!") + "), ";
        }
        public void run() {
            while (countDown-- > 0) {
                System.out.print(status()); 
                Thread.yield(); //后面解释
            }
        }
    }
    
    public class BasicThreads {
        public static void main(String[] args) {
            //把任务装进线程里
            Thread t = new Thread(new LiftOff(10));
            t.start();
            System.out.println("Waiting for LiftOff");
        }
    }
    ```

  - Runnable的内容就是线程的载荷

- 或者直接为线程添加run方法

  - ```java
    ublic class SimpleThread extends Thread {
        private int countDown = 5;  private static int threadCount = 0;
        public SimpleThread() {
            super(Integer.toString(++threadCount));  start();
        }
        public String toString() {
            return "#" + getName() + "(" + countDown + "), ";
        }
        public void run() {
            while (true) { System.out.print(this);  if (--countDown == 0) return; }
        }
        public static void main(String[] args) {
            for (int i = 0; i < 5; i++) { new SimpleThread(); }
        }
    }
    //更简洁的写法
    public class MoreBasicThreads {
        public static void main(String[] args) {
            for (int i = 0; i < 5; i++)
                new Thread(new LiftOff(10)).start();
            System.out.println("Waiting for LiftOff");
        }
    } 
    ```

- 使用线程池

  - ```java
    //结合线程池
    public class CachedThreadPool {
        public static void main(String[] args) {
            ExecutorService exec = Executors.newCachedThreadPool();
            for (int i = 0; i < 5; i++)
                exec.execute(new LiftOff(10));
            exec.shutdown();
        }
    }
    ```

- 睡眠`TimeUnit.MILLISECONDS.sleep(100);`

- 让位`Yield`

  - 向调度程序**提示当前线程愿意放弃其当前对处理器的使用**。(只是一种启发性质的建议)
  - yield会**临时暂停当前线程**，让有**同样优先级**的正在等待的线程有机会执行
  - 若没有正在等待的线程或者所有正在等待的线程的**优先级都较低，则继续运行**
  - 执行yield的线程何时继续运行由线程调度器来决定，不同厂商可能有不同行为
  - yield方法**不保证当前的线程会暂停或者停止**，但是可以保证当前线程在调用yield方法时会放弃CPU

- 线程优先级

  - `Thread.currentThread().setPriority(priority);`
  - 常量`Thread.MIN_PRIORITY Thread.MAX_PRIORITY `
  - 尽量不要做

- 守护(Daemon)进程

  - 守护程序线程是一种线程，它**不会阻止 JVM 在程序完成**但**线程仍在运行时退出**。

  - ```java
    Thread daemon = new Thread(new SimpleDaemons());
    daemon.setDaemon(true); // Must call before start()
    daemon.start();
    ```

#### 线程控制

- join等待线程（等待另一个线程完成之后在继续）

  - ```java
    class Joiner extends Thread {
        private Sleeper sleeper;//一个自定义线程类型
        public Joiner(String name, Sleeper sleeper) {
            super(name);
            this.sleeper = sleeper;
            start();
        }
        public void run() {
            try {
                sleeper.join();
            } catch (InterruptedException e) {
                System.out.println("Interrupted");
            }
            System.out.println(getName() + " join completed");
        }
    }
    ```

- 直接使用锁

  - ```java
    public int next() {
            //加锁
            lock.lock();  // block until condition holds
            try {
                ++currentEvenValue;
                Thread.yield();
                ++currentEvenValue;
                return currentEvenValue;
            } finally {
                lock.unlock();   //一定要用try-catch的finally去释放锁
            }
        }
    ```

  - 更高级的锁``ReentrantLock``

    - 如果一个线程已经持有了锁，**它可以再次请求并获得锁而不会被阻塞**。`ReentrantLock` 会维护一个持有锁的计数器来跟踪锁的重入**次数**，线程每请求一次锁，计数器就增加一，每释放一次锁，计数器就减一。当计数器归零时，锁被释放。

- **wait()**:

  - 当一个线程调用 `wait()` 时，它会**释放当前持有的监视器锁**，并暂停执行，直到另一个线程调用相同对象的 `notify()` 或 `notifyAll()` 方法。
  - 调用 `wait()` 后，当前线程进入**对象的等待集**（wait set），处于阻塞状态，直到被通知（或中断）。

- **notify()**:

  - `notify()` 方法用于**唤醒**在此对象监视器上等待的单个线程。
  - 如果有多个线程都在等待，那么会**随机选择一个线程进行唤醒**。被唤醒的线程将尝试**重新获取对象监视器的锁**。

- **notifyAll()**:

  - `notifyAll()` 方法用于**唤醒在此对象监视器上等待的所有线程。**
  - 一旦调用 `notifyAll()`，所有处于等待集中的线程都会被唤醒，然后竞争尝试重新获取对象监视器的锁。

- 例子：汽车打蜡

  - ```java
    class Car {
        private boolean waxOn = false;
    
        public synchronized void wax() {
            System.out.println("Wax On by " + Thread.currentThread().getName());
            waxOn = true;
            notifyAll();
        }
        public synchronized void buff() {
            System.out.println("Wax Off by " + Thread.currentThread().getName());
            waxOn = false;
            notifyAll();
        }
    
        public synchronized void waitForWaxing() throws InterruptedException {
            while (waxOn == false)
                wait();
        }
        public synchronized void waitForBuffing() throws InterruptedException {
            while (waxOn == true)
                wait();
        }
    }
    
    class WaxOn implements Runnable {
        private Car car;
        private String name;
    
        public WaxOn(Car c, String name) {
            this.car = c;
            this.name = name;
        }
    
        public void run() {
            Thread.currentThread().setName(name);
            try {
                while (!Thread.interrupted()) {
                    car.waitForBuffing();
                    TimeUnit.MILLISECONDS.sleep(200);
                    car.wax();
                }
            } catch (InterruptedException e) {
                System.out.println("Exiting via interrupt");
            }
            System.out.println("Ending Wax On task");
        }
    }
    class WaxOff implements Runnable {
        private Car car;
        private String name;
    
        public WaxOff(Car c, String name) {
            this.car = c;
            this.name = name;
        }
    
        public void run() {
            Thread.currentThread().setName(name);
            try {
                while (!Thread.interrupted()) {
                    car.waitForWaxing();
                    TimeUnit.MILLISECONDS.sleep(500);
                    car.buff();
                }
            } catch (InterruptedException e) {
                System.out.println("Exiting via interrupt");
            }
            System.out.println("Ending Wax Off task");
        }
    }
    public class WaxOMatic {
        public static void main(String[] args) throws Exception {
            Car car = new Car();
            ExecutorService exec = Executors.newCachedThreadPool();
            exec.execute(new WaxOff(car, "A-OFF"));
            exec.execute(new WaxOn(car, "B-ON"));
            exec.execute(new WaxOn(car, "C-ON"));
    
            TimeUnit.SECONDS.sleep(5); // Run for a while...
            exec.shutdownNow(); // Interrupt all tasks
        }
    }
    ```
    
  - 出现了问题！因为检查之后放弃了锁，在这个短暂的空闲会出问题！
  
- 线程的局部变量

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231225004728366.png" alt="image-20231225004728366" style="zoom:33%;" />

  - 线程局部变量，即这些变量**对于每个使用它的线程都有独立初始化的副本**。

  - **定义一个ThreadLocal变量**:`private static ThreadLocal<MyObject> myThreadLocal = new ThreadLocal<>();`

    - `ThreadLocal` 对象不需要一定要在使用它的线程的托管类（即实现 `Runnable` 接口或继承自 `Thread` 类的类）内部定义。
    - `ThreadLocal` 实例**可以定义在任何地方**，只要它对将要使用它的线程可见即可。关键是每个线程都可以通过这个 `ThreadLocal` 实例访问到其独立初始化的副本。

  - 设置值`myThreadLocal.set(new MyObject());`

  - 获取`MyObject obj = myThreadLocal.get();`

  - 清理`myThreadLocal.remove();`

  - ```java
    class Accessor implements Runnable {
        private final int id;
    
        public Accessor(int idn) {
            id = idn;
        }
    
        public void run() {
            while (!Thread.currentThread().isInterrupted()) {
                ThreadLocalVariableHolder.increment();
                System.out.println(this);
                Thread.yield();
            }
        }
    
        public String toString() {
            return "#" + id + ": " + ThreadLocalVariableHolder.get();
        }
    }
    public class ThreadLocalVariableHolder {
        private static ThreadLocal<Integer> value = new ThreadLocal<Integer>() {
            private Random rand = new Random(47);
            protected synchronized Integer initialValue() {
                return rand.nextInt(10000);
            }
        };
    
        public static void increment() {
            value.set(value.get() + 1);
        }
    
        public static int get() {
            return value.get();
        }
    
        public static void main(String[] args) throws Exception {
            ExecutorService exec = Executors.newCachedThreadPool();
            for (int i = 0; i < 5; i++)
                exec.execute(new Accessor(i));
            TimeUnit.SECONDS.sleep(3);  // Run for a while
            exec.shutdownNow();         // All Accessors will quit
        }
    }
    ```


- `CountDownLatch`

  - 用于在一组线程之间进行同步，它允许一个或多个线程等待直到在其他线程中进行的一组操作完成。

  - `CountDownLatch` 在创建时被初始化为一个给定的计数值（称为“计数”）。这个计数是指需要等待完成的操作数量。`CountDownLatch latch = new CountDownLatch(N); // N是计数值`

  - **countDown() 方法**:每次调用 `countDown()` 方法都会将计数减少一。这通常在某个操作完成后被调用。

  - **await() 方法**:一个或多个线程调用 `await()` 方法会使这些线程在 `CountDownLatch` 上等待，直到计数达到零。

  - ```java
    //N个等1个
    class Driver { // ...
        void main() throws InterruptedException {
            CountDownLatch startSignal = new CountDownLatch(1);
            CountDownLatch doneSignal = new CountDownLatch(N);
    
            for (int i = 0; i < N; ++i) // create and start threads
                new Thread(new Worker(startSignal, doneSignal)).start();
    
            doSomethingElse();            // don't let run yet
            startSignal.countDown();      // let all threads proceed
            doSomethingElse();
            doneSignal.await();           // wait for all to finish
        }
    }
    class Worker implements Runnable {
        private final CountDownLatch startSignal;
        private final CountDownLatch doneSignal;
        Worker(CountDownLatch startSignal, CountDownLatch doneSignal) {
            this.startSignal = startSignal;
            this.doneSignal = doneSignal;
        }
        public void run() {
            try {
                startSignal.await();
                doWork();
                doneSignal.countDown();
            } catch (InterruptedException ex) {} // return;
        }
    
        void doWork() { ... }
    }
    //1个等N个
    class Driver2 { // ...
        void main() throws InterruptedException {
            CountDownLatch doneSignal = new CountDownLatch(N);
            Executor e = ...
    
                for (int i = 0; i < N; ++i) // create and start threads
                    e.execute(new WorkerRunnable(doneSignal, i));
    
            doneSignal.await();           // wait for all to finish
        }
    }
    class WorkerRunnable implements Runnable {
        private final CountDownLatch doneSignal;
        private final int i;
        WorkerRunnable(CountDownLatch doneSignal, int i) {
            this.doneSignal = doneSignal;
            this.i = i;
        }
        public void run() {
            try {
                doWork(i);
                doneSignal.countDown();
            } catch (InterruptedException ex) {} // return;
        }
    
        void doWork() { ... }
    }
    ```



### 线程安全的对象&集合

#### 原子类

- 原子性：
  - 原子操作是指不会被线程调度器**中断**的操作
  - 比如++就有读取、写入等多个基本操作组成，并不具有原子性
  - 及时满足原子性不代表不会出问题：
    - 可见性：一个任务进行的修改，是否对另一个任务是立即可见的

- `java.util.concurrent.atomic`包中包含了一系列的类，这些类提供了原子操作的能力。可以在多线程环境下安全地操作值，而无需使用`synchronized`关键字。

- `AtomicIntege`、`AtomicReference`等一系列对象

- 一定程度上替代stnchroized，原子对象可以在多线程环境进行无锁的**线程安全操作**，实现了原子性可见性

  - ``` java
    if(xxx)
        xxx
    ```

    - 比如判断原子对象的值和操作之间就可能储问题！这时还是要使用线程锁

  - 即多对象多操作时不能仅仅依赖源自对象

- 作为共享变量时的**优先**考虑

- 原子类型保证了当多个线程同时更新同一个变量时的线程安全。这意味着**每次操作都是完整的**，不会被其他线程的操作中断。

#### 线程安全集合

- `ConcurrentHashMap` 是 `HashMap` 的线程安全版本。它通过使用分段锁（锁分离技术）提供更高的并发性。

  - 要注意的是读取方看不到正在进行的修改
- `ConcurrentSkipListMap`: 键值对存储，类似于 `TreeMap`，但线程安全。基于跳表的并发集合，提供了一种可排序的并发集合实现。

  - `ConcurrentSkipListSet`: 类似于 `TreeSet` 的并发变体。

- `ConcurrentLinkedQueue`: 一个高效的线程安全无界队列。**非阻塞**算法，适用于高并发情况

  - `ConcurrentLinkedDeque`: 双端队列版本，支持两端的插入和移除。

- `BlockingQueue` 是一个队列，支持**阻塞的插入和移除**操作。常见的实现有 `ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`, `SynchronousQueue` 等。
- `DelayQueue`用于放置实现了 `Delayed` 接口的元素，其中的元素只能在其指定的**延迟过期**后才能从队列中取走。

  - ``` java
    class DelayedElement implements Delayed {
        private final long delayTime; // 延迟时间
        private final long expire;  // 到期时间
        public DelayedElement(long delay, TimeUnit unit) {
            this.delayTime = TimeUnit.MILLISECONDS.convert(delay, unit);
            this.expire = System.currentTimeMillis() + delayTime;
        }
    	//距离到期的时间
        @Override
        public long getDelay(TimeUnit unit) {
            long diff = expire - System.currentTimeMillis();
            return unit.convert(diff, TimeUnit.MILLISECONDS);
        }
    	//比较延迟
        @Override
        public int compareTo(Delayed o) {
            DelayedElement that = (DelayedElement) o;
            return Long.compare(this.expire, that.expire);
        }
    }
    ```
- 无锁集合的实现策略

  - 复制策略：
    - 修改是在部分数据结构的一个单独副本上进行的。只有修改完成后才会与主数据结构进行交换，然后读取方才能看见修改。
    - 注意读取放看不到未完成的修改
  - CAS策略：
    - 从内存中取出一个值，计算新值的同时保存旧值，计算完成后笔算旧值是否与当前内存中的值相同，如果不相同就读取新值并进行重复计算（内存被修改了）

### 并行流（推荐）

- `.parallel()`将流转化为一个并行流，对于大数据集，使用并行流可以显著提高性能。

  - ``` java
    public class ParallelPrime {
      static final int COUNT = 100_000;
      public static boolean isPrime(long n) {
        return rangeClosed(2, (long)Math.sqrt(n))
          .noneMatch(i -> n % i == 0);
      }
      public static void main(String [] args)
        throws IOException {
        Timer timer = new Timer();
        List <String> primes =
          iterate(2, i -> i + 1)
            .parallel()                       // [1]
            .filter(ParallelPrime:: isPrime)
            .limit(COUNT)
            .mapToObj(Long:: toString)
            .collect(Collectors.toList());
        System.out.println(timer.duration());
        Files.write(Paths.get("primes.txt"), primes,
          StandardOpenOption.CREATE);
      }
    }
    ```

- 流的并行化会将输入数据拆分为多个片段，针对独立片段可以使用各种算法

  - 数组的切分非常轻量均匀
  - 但是链表的切分只会拆分为第一个元素和剩余部分
  - 因此range（无状态生成器）并行可以加速计算
  - 但是iterate（迭代生成器）不能被很好的划分，相反使用并发反而会拖慢程序的执行

- 不要盲目使用`parallel`并不总是能让程序运行更快

- 使用并行流生成随即顺序序列（这是由于并行后读取顺序不确定）

  - ``` java
    List <Integer> x = IntStream.range(0, 30)
          .limit(10)
          .parallel()
          .boxed()
          .collect(Collectors.toList());
    ```

### 创建和运行任务

#### Task和Executor(过时)

- Executor
  - `execute(Runnable command)`: 接收一个 `Runnable` 对象，并异步地执行它。

- ExecutorService是`Executor` 的一个子接口，提供了更丰富的功能
  - `submit(Runnable/Callable task)`: 提交一个 `Runnable` 任务用于执行，并返回一个代表该任务的 `Future`。
    - 用于提交单个任务，如果无返回值Future会get到null
    - 提交后会立刻返回，尽管可能没有计算完成（即get时还要等待）
  - `invokeAll(Collection<? extends Callable<T>> tasks)`: 执行给定的**任务集合。**
    - 返回值为Future对象，任务**都执行完成**invoke才会返回值
  - `shutdown()`: 启动一次顺序关闭，**执行以前提交的任务，但不接受新任务。(并不是直接关闭)**，之后再提交任务会抛出RejectedExecutionException异常
    - `exec.isTerminated()`检查是否执行器已经关闭并且所有任务执行完成
  - `shutdownNow()`: 尝试**停止**所有正在执行的活动任务，并暂停处理正在等待的任务（直接关闭）。
- 要注意的是并不是只能使用函数接口Callable以及Runable可以使用其它lambda以及方法引用作为参数传递给submit等
- Future*（已过时）
  - 代表一个异步计算的结果
  - `cancel(boolean mayInterruptIfRunning)`: 尝试取消任务的执行。
  - `isCancelled()`: 返回任务是否被取消。
  - `isDone()`: 返回任务是否已完成。
  - `get()`: **等待**计算完成，然后检索其结果。
  - `get(long timeout, TimeUnit unit)`: 如果必要，最多等待指定的时间以获取计算结果。

- 挂起线程

  - ``` java
    public class Nap {
      public Nap(double t) { // Seconds
        try {
          TimeUnit.MILLISECONDS.sleep((int)(1000 * t));
        } catch(InterruptedException e) {
          throw new RuntimeException(e);
        }
      }
      public Nap(double t, String msg) {
        this(t);
        System.out.println(msg);
      }
    }
    
    public class NapTask implements Runnable {
      final int id;
      public NapTask(int id) { this.id = id; }
      @Override public void run() {
        new Nap(0.1); // Seconds
        System.out.println(this + " " +
          Thread.currentThread().getName());
      }
      @Override public String toString() {
        return "NapTask [" + id + "]";
      }
    }
    ```

  - ` TimeUnit.MILLISECONDS.sleep((int)(1000 * t));`会挂起线程，操作系用会在时间到达后唤醒线程并继续分配给处理器时间

- 执行任务

  - ``` java
    public static void main(String [] args) {
        //工厂方法创建单线程执行持（所有任务顺序执行）
        ExecutorService exec =
          Executors.newSingleThreadExecutor();
        IntStream.range(0, 10)
            //从数字创建挂起任务
          .mapToObj(NapTask:: new)
            //交给执行器
          .forEach(exec:: execute);
        System.out.println("All tasks submitted");
        //不再接受
        exec.shutdown();
        //每 0.1s 检查一次
        while(! exec.isTerminated()) {
          System.out.println(
            Thread.currentThread().getName() +
            " awaiting termination");
          new Nap(0.1);
        }
      }
    
    /* Output:
    All tasks submitted
    main awaiting termination
    main awaiting termination
    NapTask [0] pool-1-thread-1
    main awaiting termination
    NapTask [1] pool-1-thread-1
    NapTask [2] pool-1-thread-1
    main awaiting termination
    NapTask [3] pool-1-thread-1
    main awaiting termination
    main awaiting termination
    NapTask [4] pool-1-thread-1
    NapTask [5] pool-1-thread-1
    main awaiting termination
    NapTask [6] pool-1-thread-1
    main awaiting termination
    main awaiting termination
    NapTask [7] pool-1-thread-1
    main awaiting termination
    NapTask [8] pool-1-thread-1
    main awaiting termination
    NapTask [9] pool-1-thread-1
    */
    ```

- 使用更多线程
  - `ExecutorService exec Executors.newCachedThreadPool();`
  - 小心副作用的影响，多个进程修改用一个变量(可变共享状态)会出现竞争

- 生成结果

  - 避免使用可变共享状态

  - 自私儿童原则：什么都不共享

  - 使用Callable而不是Runable（即尽可能使用返回值作为结果，而不是用副作用即直接在方法中进行修改）

  - ``` java
    //无副作用的方法
    public class CountingTask implements Callable <Integer> {
      final int id;
      public CountingTask(int id) { this.id = id; }
      @Override public Integer call() {
        Integer val = 0;
        for(int i = 0; i < 100; i++)
          val++;
        System.out.println(id + " " +
          Thread.currentThread().getName() + " " + val);
        return val;
      }
    }
    
    public class CachedThreadPool3 {
      public static Integer
      extractResult(Future <Integer> f) {
        try {
          return f.get();
        } catch(Exception e) {
          throw new RuntimeException(e);
        }
      }
      public static void main(String [] args)
        throws InterruptedException {
        ExecutorService exec =
          Executors.newCachedThreadPool();
          //构建任务集合
        List <CountingTask> tasks =
          IntStream.range(0, 10)
            .mapToObj(CountingTask:: new)
            .collect(Collectors.toList());
          //执行并存储结果
        List <Future<Integer> > futures =
          exec.invokeAll(tasks);
        Integer sum = futures.stream()
            //会自动等待执行完成在获取结果并计算
          .map(CachedThreadPool3:: extractResult)
          .reduce(0, Integer:: sum);
        System.out.println("sum = " + sum);
        exec.shutdown();
      }
    }
    //使用流优雅解决
    public static void main(String [] args) {
        System.out.println(
          IntStream.range(0, 10)
            .parallel()
            .mapToObj(CountingTask:: new)
            .map(ct -> ct.call())
            .reduce(0, Integer:: sum));
      }
    ```

- 终止长时间运行的任务

  - 提前终止任务

  - 最好的方案是在任务内定期检查某个标识，并自己关闭流程终止

    - 使用AtomicBoolean作为标志，避免并发问题

    - ``` java
      public class QuittableTask implements Runnable {
        final int id;
        public QuittableTask(int id) { this.id = id; }
        private AtomicBoolean running =
          new AtomicBoolean(true);
        public void quit() { running.set(false); }
        @Override public void run() {
            //跳出循环即终止
          while(running.get())
            new Nap(0.1);
          System.out.print(id + " ");
        }
      }
      ```

  - 可以在外面调用定义的quiet实现告知方法进行关闭，这笔强制关闭更加安全

#### CompletableFuture（推荐）

- 功能
  - **执行长时间运行的任务而不阻塞主线程**： 如果你有耗时的任务，比如从网络加载数据、执行复杂计算或访问数据库，你可以使用 `CompletableFuture` 来异步执行这些任务，避免阻塞主线程。
  - **任务结果的链式处理**： 你可以对 `CompletableFuture` 使用一系列的方法（比如 `thenApply`, `thenAccept`, `thenCompose`）来处理异步任务的结果，这些方法可以顺序执行，形成一个处理流程。
  - **组合多个异步任务**： `CompletableFuture` 可以组合多个异步任务的结果。例如，你可以并行执行多个任务，并在它们都完成时获得最终结果。

- 可以直接使用CompletableFuture来管理项目，不需要再使用ExecutorService

- 创建CompletableFuture对象

  - 特殊类型`CompletableFuture<void>`只用于任务完成或失败时的提示
  - `CompletableFuture<X> x = CompletableFuture.completedFuture(X类型对象)`获取一个已经完成的对象(传入的对象就作为结果)
    - 不传入任何参数表示创建一个未完成的新对象

  - 手动（正常）完成`x.complete(Result)`如果 CompletableFuture 已经被完成（无论是正常完成、异常完成还是被取消），该方法不会改变其状态或结果。
    - 只有用于未完成的才会被正确完成并返回true
    - `obtrudeValue(T value)`用于强行更改 `CompletableFuture` 的结果，无论它之前是否已经完成，甚至是异常完成或被取消。

  - 取消任务`x.cancel(true)`

- 检查任务执行状况

  - `isDone()`检查 `CompletableFuture` 是否已完成，**无论**是正常完成、异常完成还是被取消。
  - `isCancelled()`检查 `CompletableFuture` 是否被取消。
  - `isCompletedExceptionally()`是否异常完成，即是否在执行过程中抛出了异常。
    - 插入异常到CompletableFuture`.completeException(new Exception());`

- 获取执行结果

  - `getNow(T valueIfAbsent)`尝试获取 `CompletableFuture` 的完成值。如果 `CompletableFuture` 尚未完成，则返回给定的默认值。
  - `join()`与 `get()` 类似，但 `join()` 抛出的是未经检查的异常，使其更适合于lambda表达式。

- 执行任务

  - `CompletableFuture<Void> runAsync(Runnable runnable)`
  - 具有不同函数接口的许多版本`supplyAsync`、`AcceptAsync`等

- 常用的函数接口及含义

  - `Runnable`：没有参数和返回值的。`runAsync`
  - `Consumer`：接受单个输入参数并且不返回结果的操作。它主要用于对参数进行操作或副作用。`AcceptAsync`
  - `Function`：有一个参数，并且有返回值，且类型可以不同，用于转换异步操作的结果。`ApplyAsync`
  - `Supplier`：没有输入但返回一个结果的函数，用于提供异步操作的结果。`supplyAsync`

- 添加链式执行方法

  - `CompletableFuture<Void> thenRunAsync(Runnable action)`根据参数和返回值不同有不同函数接口的版本`thenApplyAsync`等等

  - 获取依赖数目`getNumberOfDependents()`即注册的依赖（后续操作）的数目

  - `thenCompose` 方法用于将两个异步操作串联起来，将一个异步操作的结果作为另一个异步操作的输入。

      - ``` java
          CompletableFuture <Void> future = getUserEmail(userId)
              .thenCompose(email -> sendEmail(email, message));
          ```

      - `getUserEmail` 返回一个包含用户电子邮件的 `CompletableFuture`。然后，`thenCompose` 使用这个电子邮件地址来调用 `sendEmail` 方法，后者也返回一个 `CompletableFuture`。

  - ``` java
      public class CompletableApply {
        public static void main(String [] args) {
          CompletableFuture <Machina> cf =
            CompletableFuture.completedFuture(
              new Machina(0));
          CompletableFuture <Machina> cf2 =
            cf.thenApply(Machina:: work);
          CompletableFuture <Machina> cf3 =
            cf2.thenApply(Machina:: work);
          CompletableFuture <Machina> cf4 =
            cf3.thenApply(Machina:: work);
          CompletableFuture <Machina> cf5 =
            cf4.thenApply(Machina:: work);
        }
      }
      //更简洁的写法
      CompletableFuture <Machina> cf =
            CompletableFuture.completedFuture(
              new Machina(0))
            .thenApply(Machina:: work)
            .thenApply(Machina:: work)
            .thenApply(Machina:: work)
            .thenApply(Machina:: work);
      ```

  - 默认情况下需要等所有转化都完成创建过程才会结束

    - 使用`Async`为异步方法，即**立即返回对象**，在后台继续异步完成创建

- 合并

  - 接受两个CompletableFuture操作并以多种方式合并

  - ``` java
    public static void main(String [] args) {
        /*任何一个完成之后就执行方法
        Workable [BW]
    	runAfterEither
    	Workable [AW]*/
        voidr(cfA.runAfterEitherAsync(cfB, () -> System.out.println("runAfterEither")));
    	/*Workable [BW]
    	Workable [AW]
    	runAfterBoth*/
        voidr(cfA.runAfterBothAsync(cfB, () -> System.out.println("runAfterBoth")));
    	/*Workable [BW]
    	applyToEither: Workable [BW]
    	Workable [BW]（返回值）
    	Workable [AW]（A 也完成了）*/
        showr(cfA.applyToEitherAsync(cfB, w -> {
            System.out.println("applyToEither: " + w);
            return w;
        }));
    	/*Workable [BW]
    	acceptEither: Workable [BW]
    	Workable [AW]*/
        voidr(cfA.acceptEitherAsync(cfB, w -> {
            System.out.println("acceptEither: " + w);
        }));
    	/*Workable [BW]
    	Workable [AW]
    	thenAcceptBoth: Workable [AW], Workable [BW]*/
        voidr(cfA.thenAcceptBothAsync(cfB, (w1, w2) -> {
            System.out.println("thenAcceptBoth: "
                               + w1 + ", " + w2);
        }));
    	/*Workable [BW]
    	Workable [AW]
    	thenCombine: Workable [AW], Workable [BW]
    	Workable [AW]*/
        showr(cfA.thenCombineAsync(cfB, (w1, w2) -> {
            System.out.println("thenCombine: "+ w1 + ", " + w2);
            return w1;
        }));
    	//任何一个完成
        CompletableFuture <Workable>
            cfC = Workable.make("C", 0.08),
        cfD = Workable.make("D", 0.09);
        CompletableFuture.anyOf(cfA, cfB, cfC, cfD)
            .thenRunAsync(() -> System.out.println("anyOf"));
    	//全部完成
        cfC = Workable.make("C", 0.08);
        cfD = Workable.make("D", 0.09);
        CompletableFuture.allOf(cfA, cfB, cfC, cfD)
            .thenRunAsync(() ->
                          System.out.println("allOf"));
    }
    ```

  - 以做蛋糕进行模拟

    - ``` java
      //制作面糊
      public class Batter {
        static class Eggs {}
        static class Milk {}
        static class Sugar {}
        static class Flour {}
        static <T> T prepare(T ingredient) {
          new Nap(0.1);
          return ingredient;
        }
        static <T> CompletableFuture <T> prep(T ingredient) {
          return CompletableFuture
            .completedFuture(ingredient)
            .thenApplyAsync(Batter:: prepare);
        }
        public static CompletableFuture <Batter> mix() {
          CompletableFuture <Eggs> eggs = prep(new Eggs());
          CompletableFuture <Milk> milk = prep(new Milk());
          CompletableFuture <Sugar> sugar = prep(new Sugar());
          CompletableFuture <Flour> flour = prep(new Flour());
          CompletableFuture
            .allOf(eggs, milk, sugar, flour)
            .join();//等待全部完成
          new Nap(0.1); // Mixing time
          return
            CompletableFuture.completedFuture(new Batter());
        }
      }
      
      public class Baked {
        static class Pan {}
        static Pan pan(Batter b) {
          new Nap(0.1);
          return new Pan();
        }
        static Baked heat(Pan p) {
          new Nap(0.1);
          return new Baked();
        }
        static CompletableFuture <Baked>
        bake(CompletableFuture <Batter> cfb) {
          return cfb
            .thenApplyAsync(Baked:: pan)
            .thenApplyAsync(Baked:: heat);
        }
        public static
        Stream <CompletableFuture<Baked> > batch() {
          CompletableFuture <Batter> batter = Batter.mix();//获取制作好的面糊
          return Stream.of(bake(batter), bake(batter),
                           bake(batter), bake(batter));
        }
      }
      
      final class Frosting {
        private Frosting() {}
        static CompletableFuture <Frosting> make() {
          new Nap(0.1);
          return CompletableFuture
            .completedFuture(new Frosting());
        }
      }
      
      public class FrostedCake {
        public FrostedCake(Baked baked, Frosting frosting) {
          new Nap(0.1);
        }
        @Override public String toString() {
          return "FrostedCake";
        }
        public static void main(String [] args) {
          Baked.batch().forEach(baked -> baked//获取 stream
            .thenCombineAsync(Frosting.make(),//新制作的糖霜
              (cake, frosting) ->
                new FrostedCake(cake, frosting))//创建蛋糕
            .thenAcceptAsync(System.out:: println)
            .join());
        }
      }
      ```

- 异常处理

  - 会缓存异常，并且只在尝试提取结果时体现出来，可以使用trycatch处理，但是有更高级的异常处理机制

  - `exceptionally` 用于处理 `CompletableFuture` 中发生的异常。它提供了一种恢复机制，允许你返回一个默认值或执行一些替代操作。

    - 只有在出现异常时才会被调用

    - ``` java
      .exceptionally((ex) -> {
          if(ex == null)
              System.out.println("I don't get it yet");
          return new Breakable(ex.getMessage(), 0);
      })
      ```

  - `handle` 方法用于处理 `CompletableFuture` 的结果或异常。它接收两个参数：结果和异常。根据这两个参数，你可以决定如何处理成功或失败的情况。

    - ``` java
      .handle((result, fail) -> {
          if(fail != null)
              return "Failure recovery object";
          else
              return result + " is good";
      })
      ```

  - `whenComplete` 方法用于在 `CompletableFuture` **完成时**执行一些操作，无论是正常完成还是异常结束。它不改变 `CompletableFuture` 的结果。

    - ``` java
      .whenComplete((result, fail) -> {
          if(fail != null)
              System.out.println("It failed");
          else
              System.out.println(result + " OK");
      })
      ```

  - `handle`和`whenCpmlete`方法无论发生异常与否总是会被调用，区别是handle可以进行修改并且有返回值

  - 检查型异常：不能直接在 `CompletableFuture` 或 `Stream` 的lambda表达式中抛出检查型异常，除非你捕获并处理它们(即自己抛出自己处理)，或者将它们转换为非检查型异常(RuntimeException)。

    - ``` java
      CompletableFuture.supplyAsync(() -> {
          throw new IOException(); // 编译错误
      });
      CompletableFuture.supplyAsync(() -> {
          throw new IOException(); // 编译错误
      });
      ```

- 预留结合，批量创建

  - ``` java
    @Override public void run() {
        while(! generator.isCanceled()) {
          int val = generator.next();
          if(val % 2 != 0) {
            System.out.println(val + " not even!");
            generator.cancel(); // Cancels all EvenCheckers
          }
        }
      }
      // Test any IntGenerator:
      public static void test(IntGenerator gp, int count) {
        List <CompletableFuture<Void> > checkers =
          IntStream.range(0, count)
            .mapToObj(i -> new EvenChecker(gp, i))
            .map(CompletableFuture:: runAsync)//自动创建 CF 对象
            .collect(Collectors.toList());
        checkers.forEach(CompletableFuture:: join);
      
    ```

## I/O

- 绑定字符流到控制台`BufferedReader br = new BufferedReader(new InputStreamReader(System.in));`
  - 可以使用 read() 方法从控制台读取一个字符，或者用 readLine() 方法读取一个字符串。

### 控制台输入输出

- 输入Scanner
  - 引入`import java.util.Scanner;`
  - 实例`Scanner sc = new Scanner(System.in);`
  - 获取Int`int age = sc.nextInt();`
    - long等类型类似
  - 获取字符串`String name = sc.next();`
  - 获取一行`nextLine`
  
- 输出`System.out.println(...);`
  - 也可以格式化输出`System.out.printf("%d==%d %b%n",x,y,x==y)`
  
- Console

  - 封装好的控制台类，支持更加方便的功能

  - ```java
    public class Password {
        
        public static void main (String args[]) throws IOException {
    
            Console c = System.console();
            if (c == null) {
                System.err.println("No console.");
                System.exit(1);
            }
    
            String login = c.readLine("Enter your login: ");
            char [] oldPassword = c.readPassword("Enter your old password: ");
            ...
        }
    }
    ```


### IO流

#### 传统IO

##### InputStream

- ByteArrayInputStream：将内存缓冲区充当InputStream
- StringBufferInputStream：将字符串转化为InputStream
- FileInputStream：从文件读取信息
- PipedInputStream：生成写入到对应PipedOutStream的数据，实现管道传输
- SequenceInputStream：将两个以上的InputStream转化为单个InputStream
- FilterInputStream：作为装饰器接口抽象类，为其他InputStream提供功能
  - DataInputStream：从流读取基本类型，有方法如readFloat()等
  - Buffered-InputStream：声明使用缓冲区

##### OutputStream

- ByteArrayOutputStream：在内存创建一块缓冲区，发送到流的数据被存储在缓冲区
- FileOutStream：用于向文件发送信息
- PipedOutStream：向其中写入的任何信息作为PipedInputStream的输入（双向管道传输）
- FilterOutStream：装饰器
  - DataOutputStream：writeFloat等
  - PrintStream：生成格式化输出，即用于处理数据的显示
  - BufferedOutStream：声明使用缓冲区

##### Reader和Writer

- Input/OutStream类提出了面向字节的IO能力，ReadWrite提供了兼容Unicode并且兼容字符的IO能力。
- 适配器InputStreamReader和OutputStreamWriter可以将Input/OutputStream转化为Reader/Writer
  - 都有对应的，后缀为R/W

##### Scanning

- 用于扫描和解析文本数据

- 可以用不同的输入源来创建 `Scanner` 对象`Scanner scanner = new Scanner(System.in); // 从标准输入读取`

- `Scanner` 类提供了一系列的方法来读取不同类型的数据，如 `nextInt()`, `nextDouble()`, `nextLine()`,`hasNext()` 等。

  - ```java
    int number = scanner.nextInt();
    String str = scanner.nextLine();
    ```

- 关闭`scanner.close();`

##### Formatting

- 格式化字符串`String formatted = String.format("Name: %s, Age: %d", "Bob", 25);`

##### RandomAccessFile

- 用于处理由大小已知的记录组成的文件，可以通过seek()在各条记录上来回移动，并读取或修改记录

##### 典型用法

- 从文件输入

  - ``` java
    public static String read(String filename) {
        try(BufferedReader in = new BufferedReader(
            new FileReader(filename))) {
            return in.lines()
                .collect(Collectors.joining("\n"));
        } catch(IOException e) {
            throw new RuntimeException(e);
        }
    }
    ```

    - try-with-resource

  - ``` java
    public static void main(String [] args) throws IOException {
        StringReader in = new StringReader(BufferedInputFile.read("MemoryInput.java"));
        int c;
        while((c = in.read()) != -1)
            System.out.print((char)c);
    }
    ```

    - 按照字符读取

  - ``` java
    public static void main(String [] args) {
        try(
            //复原为字节流再用 DatInputStream 重新进行读取
            DataInputStream in = new DataInputStream(
                new ByteArrayInputStream(
                    BufferedInputFile.read(
                        "FormattedMemoryInput.java")
                    .getBytes()))
        ) {
            while(true)
                System.out.write((char)in.readByte());
        } catch(EOFException e) {
            System.out.println("\nEnd of stream");
        } catch(IOException e) {
            throw new RuntimeException(e);
        }
    }
    ```

    - 格式化读取
    - 使用`in.available!=0`判断字符读取是否终止，当然也可以使用异常进行控制

- 文件输出

  - ``` java
    public static void main(String [] args) {
        try(
            BufferedReader in = new BufferedReader(
                new StringReader(
                    BufferedInputFile.read(
                        "BasicFileOutput.java")));
            PrintWriter out = new PrintWriter(
                new BufferedWriter(new FileWriter(file)))
        ) {
            //逐行写入
            in.lines().forEach(out:: println);
        } catch(IOException e) {
            throw new RuntimeException(e);
        }
        // Show the stored file:
        System.out.println(BufferedInputFile.read(file));
    }
    ```

- 存储和回复数据

  - 使用DataOutputStream写入数据，一定可以通过DataInputStream精确的恢复数据

  - ``` java
    public static void main(String [] args) {
        try(
            DataOutputStream out = new DataOutputStream(
                new BufferedOutputStream(
                    new FileOutputStream("Data.txt")))
        ) {
            out.writeDouble(3.14159);
            out.writeUTF("That was pi");
            out.writeDouble(1.41413);
            out.writeUTF("Square root of 2");
        } catch(IOException e) {
            throw new RuntimeException(e);
        }
        try(
            DataInputStream in = new DataInputStream(
                new BufferedInputStream(
                    new FileInputStream("Data.txt")))
        ) {
            System.out.println(in.readDouble());
            // Only readUTF() will recover the
            // Java-UTF String properly:
            System.out.println(in.readUTF());
            System.out.println(in.readDouble());
            System.out.println(in.readUTF());
        } catch(IOException e) {
            throw new RuntimeException(e);
        }
    }
    ```

- 随机访问文件

  - 可以使用DataInputStream相似的接口进行数据读取写入

  - ``` java
    public class UsingRandomAccessFile {
        static String file = "rtest.dat";
        public static void display() {
            try(
                RandomAccessFile rf =
                new RandomAccessFile(file, "r")//有访问控制方法
            ) {
                for(int i = 0; i < 7; i++)
                    System.out.println(
                    "Value " + i + ": " + rf.readDouble());
                System.out.println(rf.readUTF());
            } catch(IOException e) {
                throw new RuntimeException(e);
            }
        }
        public static void main(String [] args) {
            try(//独立使用，不支持使用装饰器
                RandomAccessFile rf =
                new RandomAccessFile(file, "rw")
            ) {
                for(int i = 0; i < 7; i++)
                    rf.writeDouble(i*1.414);
                rf.writeUTF("The end of the file");
                rf.close();
                display();//顺序读取
            } catch(IOException e) {
                throw new RuntimeException(e);
            }
            try(
                RandomAccessFile rf =
                new RandomAccessFile(file, "rw")
            ) {
                rf.seek(5*8);
                rf.writeDouble(47.0001);//一个元素为 8 字节，就是对第 5 个元素进行修改
                rf.close();
                display();
            } catch(IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
    ```

#### 标准IO

- 所有的输入都可以来自标准输入，所有的输出都可以发送到标准输出，可以将多个程序串联起来

- System.in System.out System.err

  - out\err被包装成了PrintStream，但是in是为包装的InputStream，要在读取之前进行手动包装

- System.in的包装和使用

  - ``` java
    public static void main(String [] args) {
        TimedAbort abort = new TimedAbort(2);
        new BufferedReader(
            new InputStreamReader(System.in))
            .lines()
            .peek(ln -> abort.restart())
            .forEach(System.out:: println);
        // Ctrl-Z or two seconds inactivity
        // terminates the program
    }
    ```

    - 流是懒加载的，并且会随着输入自动变化（如lines获取到更多的行）
    - 如果在2秒内没有新的输入，那么程序就会被终止。

- 将System.out作为PrintWriter

  - ``` java
    public static void main(String [] args) {
        PrintWriter out =
            new PrintWriter(System.out, true);//第二个参数 autoFlush
        out.println("Hello, world");
    }//即将输出定向到标准输出
    ```

- 清空缓冲区`System.out.flush();`

- 重定向

  - `SetIn(InputStream)`、`SetOut(PrintStream)`、`SetErr(PrintStream)`

  - ``` java
    public static void main(String [] args) {
        PrintStream console = System.out;//缓存默认控制台输出对象用于后面进行恢复
        try(
          BufferedInputStream in = new BufferedInputStream(
            new FileInputStream("Redirecting.java"));
          PrintStream out = new PrintStream(
            new BufferedOutputStream(
              new FileOutputStream("Redirecting.txt")))
        ) {
          System.setIn(in);//重定向为文件读取文件输出
          System.setOut(out);
          System.setErr(out);
          new BufferedReader(
            new InputStreamReader(System.in))
            .lines()
            .forEach(System.out:: println);
        } catch(IOException e) {
          throw new RuntimeException(e);
        } finally {
          System.setOut(console);
        }
      }
    ```

  - 重定向操作字节流

- 进程控制

  - 通过标准流实现向控制台发送命令，并监视输出到控制台的信息

  - ``` java
    public class OSExecute {
      public static void command(String command) {
        boolean err = false;
        try {
            //启动一个新进程。这个新进程的命令和参数就是输入的命令字符串。
          Process process = new ProcessBuilder(
            command.split(" ")).start();//执行命令
          try(//监控输出
            BufferedReader results = new BufferedReader(
              new InputStreamReader(
                process.getInputStream()));
            BufferedReader errors = new BufferedReader(
              new InputStreamReader(
                process.getErrorStream()))
          ) {
            results.lines()
              .forEach(System.out:: println);
            err = errors.lines()
              .peek(System.err:: println)
              .count() > 0;
          }
        } catch(IOException e) {
          throw new RuntimeException(e);
        }
        if(err)
          throw new OSExecuteException(
            "Errors executing " + command);
      }
    }
    ```


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

  - ``` java
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

  - ``` java
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

    - ```- java
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

  - ``` java
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
  
  - 其它基本类型也有类似的方法
  - 可以将同一个字节序类解析为不同类型，同样的缓冲区数据，不一样的解析方式
  
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231121131949514.png" alt="image-20231121131949514" style="zoom: 33%;" />


##### 内存映射文件

- 处理因为太大而无法完整加载到内存的文件，可以根据地址把文件当作一个很大的数组来进行访问

- ```java
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

- ``` java
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

  - ``` java
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

  - ``` java
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

  - ``` java
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

- ``` java
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

    - ``` java
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

  - ``` java
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

  - ``` java
    Path start = Paths.get("/some/path");
    try (Stream <Path> stream = Files.walk(start, 3)) { // 遍历深度为 3
        stream.forEach(System.out:: println); // 打印每个文件和目录的路径
    }
    ```

  - ``` java
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

  - ``` java
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

  - ```java
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

  - ```java
    URL oracle = new URL("http://www.oracle.com/");
            BufferedReader in = new BufferedReader(
            new InputStreamReader(oracle.openStream()));
    ```

- 建立连接进行读写

  - ```java
    URL oracle = new URL("http://www.oracle.com/");
    URLConnection yc = oracle.openConnection();
    BufferedReader in = new BufferedReader(new InputStreamReader(
        yc.getInputStream()));
    OutputStreamWriter out = new OutputStreamWriter( connection.getOutputStream());
    ```

#### nio

- socket：套接字是网络上运行的两个程序之间**双向通信链路**的一个端点。套接字绑定到端口号，以便 TCP 层可以识别数据要发送到的应用程序

  - 服务器保持等待，监听套接字以等待客户端发出连接请求。

    - ```java
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

  - ``` java
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

  - ``` java
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

  - ``` java
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

  - ``` java
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

    - ``` java
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
- ``` java
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

  - ``` java
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

  - ``` java
    @Override public boolean equals(Object rval) {
        //既确保不为 null，也确保类型相同
        return rval instanceof SuccinctEquality &&
            Objects.equals(i, ((SuccinctEquality)rval).i) &&
            Objects.equals(s, ((SuccinctEquality)rval).s) &&
            Objects.equals(d, ((SuccinctEquality)rval).d);
    }
    ```

- 有继承的比较

  - ``` java
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

  - ``` java
    recoed Employee(String name, int id){}
    var bob = new Employee("",0);
    ```

  - 会自动创建规范的构造器根据传入的参数进行初始化，recoed的字段要定义在头部

  - 不能加入静态方法、字段和初始化器

- record内的方法只能读取字段，不能进行修改，定义在{}内

- record不能继承其他类，但是可以实现接口

- ``` java
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

  - ``` java
    Redwarf {
        //可以修改字段的初始化值(这不是对 final 继续进行修改，而是在对 final 进行初始化前对初始化值进行修改)
        //也可以对参数进行验证
    }
    ```

- 如果想要使用带参构造器，构造器必须静秋额复制record的签名（括号内完全一致）并且必须在函数体中对字段进行初始化

#### 使用享元自定义Collecction和Map

- 享元模式：共享对象，用于减少应用程序创建的对象数量，以减少内存占用和提高性能。

- 创建一个只读数组

  - ``` java
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

  - ``` java
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

  - ``` java
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

  - ``` java
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

  - ``` java
    public @interface Uniqueness {
      Constraints constraints()
      default @Constraints(unique = true);
    }
    ```

#### 注解处理器

- 例：读取被注解的类，通过反射查找注解标签，通过注解的id查找出已经注册的测试案例

  - ``` java
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

  -  ``` java
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

  - 这套自定义注解的意义在于提供一种**声明式的方法**来描述一个Java类**如何映射**到数据库的表和字段。通过这些注解，你可以将类和字段的元数据（例如表名、字段名、字段类型、约束等）直接嵌入到Java代码中。这种方式的好处是代码更清晰、更直观，并且可以由框架或工具在运行时动态解析，从而自动生成SQL语句或执行其他数据库相关操作。

- 注解处理器：根据注解读取文件创建SQL数据库

  - ``` java
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

  - ``` java
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

  - ``` java
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

    - ``` java
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

  - ``` java
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

- ``` java
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

  - ``` java
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

  - ``` java
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

    - ``` java
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

- 函数式接口是只包含一个抽象方法声明的接口

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

  - ``` java
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

  - ``` java
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

    - ``` java
      return in.andThen(o -> {
          System.out.println(o);
          return o;
      });
      ```

    - `f.andThen()`在f之后调用

    - `.compose()`在f之前调用

    - 会得到一个新函数

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231113004543975.png" alt="image-20231113004543975" style="zoom:50%;" />

    - ``` java
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

  - ``` java
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

  - ``` java
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

    - ``` java
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

  - ``` java
    Stream <Integer> stream = new Random().ints(0, 100).boxed().distinct();
            Iterator <Integer> iterator = stream.iterator();
    ```

  -  通过操作迭代器进行访问


#### 修改流元素

- `skip(n)`丢弃前n个元素

- 查看流对象`peek(System.out::print)`

  - 要求输入Consumer函数式接口，即没有返回值，也就不会对流修改

- `sorted([比较函数])`

  - 内置比较如`Comparator.reverseOrder()`

  - 使用lambda（创建匿名类）

    - ``` java
      List <Person> sortedByAge = people.stream()
          .sorted((p1, p2) -> Integer.compare(p1.getAge(), p2.getAge()))
          .collect(Collectors.toList());
      ```

- 移除重复元素`distinct()`

- `filter()`过滤元素

  - 过滤操作只保留传给过滤函数后返回值为true的元素

  - ``` java
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

  - ``` java
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

    - ``` java
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

  - ``` java
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

    - ``` java
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

    - ``` java
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

- ``` java
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

  - ``` shell
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

- ``` java
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

- ``` java
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