### 控制台输入输出

- 绑定字符流到控制台 `BufferedReader br = new BufferedReader(new InputStreamReader(System.in));`
  - 可以使用 read () 方法从控制台读取一个字符，或者用 readLine () 方法读取一个字符串。

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
```java
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

