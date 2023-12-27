#### 标准IO

- 所有的输入都可以来自标准输入，所有的输出都可以发送到标准输出，可以将多个程序串联起来

- System.in System.out System.err

  - out\err被包装成了PrintStream，但是in是为包装的InputStream，要在读取之前进行手动包装

- System.in的包装和使用


``` java
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


``` java
public static void main(String [] args) {
    PrintWriter out =
        new PrintWriter(System.out, true);//第二个参数 autoFlush
    out.println("Hello, world");
}//即将输出定向到标准输出
```

- 清空缓冲区`System.out.flush();`

- 重定向

  - `SetIn(InputStream)`、`SetOut(PrintStream)`、`SetErr(PrintStream)`


``` java
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


``` java
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
