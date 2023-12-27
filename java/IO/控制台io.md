### 控制台输入输出

- 绑定字符流到控制台 `BufferedReader br = new BufferedReader(new InputStreamReader(System.in));`
  - 可以使用 read () 方法从控制台读取一个字符，或者用 readLine () 方法读取一个字符串。

- 输出`System.out.println(...);`
  - 也可以格式化输出 `System.out.printf("%d==%d %b%n",x,y,x==y)`

- 使用 [[旧IO#Scanning|Scanning]] 获取更强大更强大 (格式化)的输出

- Console
  - 封装好的控制台类，支持更加方便的功能
```java
public class Password {
    
    public static void main (String args[]) throws IOException {
		//获取控制台对象
        Console c = System.console();
        if (c == null) {
            System.err.println("No console.");
            System.exit(1);
        }

        String login = c.readLine("Enter your login: ");
        //读取密码模式
        char [] oldPassword = c.readPassword("Enter your old password: ");
        ...
    }
}
```

