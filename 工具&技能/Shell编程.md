### Shell编程

- 定义变量，如`foo=bar`，服务器重启之后就会失效

  - 将命令的输出保存到变量`foo=$(pwd)`
    - `$(pwd)`将输出转化为了一个**临时变量**
  - `<( CMD )` 会执行 `CMD` 并将结果**输出到一个临时文件中**，并将 `<( CMD )` **替换成临时文件名**。
    -  `diff <(ls foo) <(ls bar)` 会显示文件夹 `foo` 和 `bar` 中文件的区别。

- 使用双引号或单引号来表示字符串，其中双引号会进行转化（如将变量替换为值），而单引号是原始字符串

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231223000025278.png" alt="image-20231223000025278" style="zoom:33%;" />

- 脚本为.sh文件

- 定义函数

  - ```shell
    mcd () {
        mkdir -p "$1"
        cd "$1"
    }
    ```

  - `$0` - 脚本名

  - `$1` 到 `$9` - **脚本的参数**。 `$1` 是第一个参数，依此类推。

  - `$@` - 所有参数

  - `$#` - 参数个数

  - `$?` - 前一个命令的**返回值**

    - 对于无返回值的成功返回0，失败返回1
    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20231223001614316.png" alt="image-20231223001614316" style="zoom:33%;" />

  - `$$` - 当前脚本的进程识别码

  - `!!` - 完整的**上一条命令**，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。

  - `$_` - 上一条命令的**最后一个参数**。如果你正在使用的是交互式 shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。

- 条件语句

  - ```shell
    if [[ "$variable" = "value" ]]; then
        echo "变量等于值"
    else
        echo "变量不等于值"
    fi
    ```

- case语句

  - ```shell
    case "$variable" in
        pattern1)
            # 如果变量匹配pattern1，执行这里的代码
            ;;
        pattern2)
            # 如果变量匹配pattern2，执行这里的代码
            ;;
        *)
            # 如果没有匹配的条件，执行这里的代码
            ;;
    esac
    ```

- 循环语句

  - ```shell
    for item in list; do
        # 循环体中的代码
    done
    
    count=1
    while [ $count -le 5 ]; do
        echo "计数：$count"
        count=$((count+1))
    done
    
    until [ condition ]; do# 条件为假时重复执行一段代码
        # 循环体中的代码
    done
    ```

- 运算符

  - 字符串连接`result="$str1.$str2"`

  - 比较

    - ```shell
      -gt # 大于
      -lt # 小于
      -ge # 大于等于
      -le # 小于等于
      ```

  - `-e`：检查**文件是否存在**。如果文件存在，则返回真，否则返回假。

  - `-f`：检查文件是否是**一个普通文件**。如果是普通文件，则返回真，否则返回假。

  - `-d`：检查文件是否是**一个目录**。如果是目录，则返回真，否则返回假。

  - `-r`：检查文件是否可读。如果文件可读，则返回真，否则返回假。

  - `-w`：检查文件是否可写。如果文件可写，则返回真，否则返回假。

  - `-x`：检查文件是否可执行。如果文件可执行，则返回真，否则返回假。

  - `-s`：检查文件是否为空。如果文件不为空，则返回真，否则返回假。

- 逻辑运算符

  - ```shell
    false || echo "Oops, fail"
    # Oops, fail
    true || echo "Will not be printed"
    #
    true && echo "Things went well"
    # Things went well
    false && echo "Will not be printed"
    #
    false ; echo "This will always run"
    # This will always run
    ```

- 执行脚本`source mcd`就将方法加载到了终端作用域，之后可以直接使用`mcd xxx`执行

- 例子

  - ```shell
    echo "Starting program at $(date)" # date会被替换成日期和时间
    
    echo "Running program $0 with $# arguments with pid $$"
    
    for file in "$@"; do
        grep foobar "$file" > /dev/null 2> /dev/null
        # 如果模式没有找到，则grep退出状态为 1
        # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
        if [[ $? -ne 0 ]]; then
            echo "File $file does not have any foobar, adding one"
            echo "# foobar" >> "$file"
        fi
    done
    ```

  - **标准输出（stdout，文件描述符 1）：** 用于向终端或其他输出设备输出数据。
  - **标准错误（stderr，文件描述符 2）：** 用于向终端或其他输出设备输出错误消息和诊断信息。

- 通配符

  - `ls *.sh`筛选文件类型

  - `ls project?`

  - ```shell
    convert image.{png,jpg}
    # 会展开为
    convert image.png image.jpg
    
    cp /path/to/project/{foo,bar,baz}.sh /newpath
    # 会展开为
    cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath
    # 下面命令会创建foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h这些文件(一个语句多个大括号使用笛卡尔积组合)
    touch {foo,bar}/{a..h}
    ```

- 其他语言编写脚本（不能作为函数）

  - ```shell
    #!/usr/local/bin/python
    import sys
    for arg in reversed(sys.argv[1:]):
        print(arg)
    ```

  - 函数只能与shell使用相同的语言，脚本可以使用任意语言。因此在脚本中包含 `shebang` 是很重要的。

  - 函数仅在定义时被加载，脚本会在每次被执行时加载。这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。

  - 函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 [`export`](https://man7.org/linux/man-pages/man1/export.1p.html) 将环境变量导出，并将值传递给环境变量。

  - 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell 脚本中往往也会包含它们自己的函数定义。

- 脚本静态代码分析工具 [shellcheck](https://github.com/koalaman/shellcheck)
  - 识别并提供关于 Shell 脚本中的语法错误、安全漏洞、代码风格问题和一般性问题的有用建议。
  - `shellcheck your_script.sh`