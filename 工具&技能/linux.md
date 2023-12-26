# linux

## 虚拟机

### 快照

- 用于虚拟机备份/还原

## 基础命令

### [vim](vim.md)

### 文件系统

- 整个系统都在根目录/下
- linux中层级用/表示，windows用\\(//)
  - 此外开头一定会有一个/ 表示根目录
  - 之后的/表示层级关系

### 基础概念

- 命令行：linux终端，一种命令提示符界面，以纯字符操作系统，发出指令。
- 命令：linux程序，可以在终端中提供字符化反馈。
- 基础格式 command [-options] [parameter]
  - command 命令本身
  - -options 命令的一些选项，控制行为细节
    - 可以组合使用，如：-l -a或-la或-al均可
  - parameter 命令的参数，多用于指向目标
- linux系统终端默认会加载HOME目录为当前的工作目录
  - HOME目录：每个Linux系统的个人账户目录（/home/用户名）
  - /home/用户名/~

- 路径

  - 绝对路径：以根目录为起点

  - 相对路径：以当前路径为起点

  - 特殊路径符
    - **.表示当前目录** cd ./Desktop等价于cd Desktop
    - **..表示上一级目录** cd ..返回上一级目录 cd../..上两级
    - ~ HOME目录

- 通配符

  - \* 匹配任意内容
  - test\* 以test开头
  - \*test 以test结尾
  - \*test\* 包含test
- | 管道符，将管道左边命令的结果作为右边命令的输入
  - 比如cat text01.txt | grep xxx 等价于 grep xxx text01.txt
  - 如查找文件 ls xx | grep xx
  - 嵌套使用xx|xx|xx|...依次向右传递

- **ctrl+c 强制停止**命令的执行

### 文件命令类似

- ls [-a -l -h] [linux路径]
  - 省缺参数时直接列出当前工作目录下的内容
  - 选项
    - -a 列出全部的文件（包含隐藏的文件和文件夹）
    - -l 以列表的形式展现详细内容
    - -h 与l一起用，-hl以kb/mb等显示文件大小
- cd [linux路径] 切换工作目录
  - 省缺参数默认为home路径
- pwd 输出当前工作目录（查看当前所处的位置）
- mkdir [-p] linux路径 新建文件夹
  - -p自动创建不存在的父目录，即连续创建多级目录
  - 创建文件需要修改权限，HOME文件夹外无法成功
- touch linux路径 创建路径
- cat linux路径 查看文件内容
- more linux路径 翻页查看
  - 空格 下一页
  - q 退出查看
- cp [-r] 路径1 路径2 复制文件（夹）
  - -r 用于复制文件夹
  - 从路径1复制到路径2
- mv 路径1 路径2 移动文件（夹）
  - 可用于重命名 mv test01.txt test02.txt
- rm [-r -f] 路径1 路径2 ...  删除
  - -r用于删除文件夹
  - -f强制删除，不弹出提示（管理员用户）
  - 一次可以删除多个
- grep [-n] 关键字 文件路径，通过关键字过滤文件行
  - -n 在结果中显示匹配的行号
  - 关键字，表示过滤的关键字，包含空格等特殊符号时用“”包住
  - 文件路径，要过滤内容的文件，可作为内容输入端口(可以由管道符输入)

- wc [-c -m -l -w] 文件路径，统计文件信息
  - -c 统计bytes数目
  - -m 统计字符数目
  - -l 统计行数
  - -w 统计单词数目
  - 默认输出 行数+单词数+字节数
  - 文件路径，要统计内容的文件，可作为内容输入端口

- which 要查找的命令（用于查找环境变量下的命令）
  - 命令如ls本质上是一个个程序
  - which用于查看which的程序文件在哪里

- find 起始路径 -name(查找方式) “名称”（用于查找文件）
  - 可以结合，通配符使用
  - find 起始路径 -size +(-)n[单位] ，按照文件的大小查找
    - \+\-表示大于和小于
    - n表示数字
    - 单位：k表示kb，M表示MB，G表示GB
    - 如 find / -size-10k

- echo 内容（可以用双引号包含）
  - 用于在命令行内输出内容
  - 用反引号`包含的内容会被作为命令执行
  - 如echo  \`ls\`

- 重定向符
  - \>将左侧命令结果覆盖写入右侧指定文件
  - \>>将左侧命令追加写入右侧文件中
  - 如echo "xxx" > text.txt

- tail [-f -num] Linux路径 ，查看文件尾部内容，跟踪文件最新更改
  - -f 表示持续跟踪
    - 会实时显示文件内容的更改（当此文件在其它终端中被修改时）

  - -num 表示查看尾部多少行，默认为10

### 用户系统	

- root用户（超级管理员）
  - 拥有最大权限
  - 普通用户一般只在home目录内不受限，在大多位置都没修改权限
- su [-] [用户名]， 用户切换
  - -表示切换用户后加载环境变量
  - 用户名默认为root
  - 切换到root需要输入密码
- exit 回退到上一个用户
  - 快捷键 ctrl+d 
- sudo 其它命令
  - 为一条命令赋予root权限
  - 只有被sudo认真的用户才能使用sudo命令
    - root用户 输入visudo 打开/etc/sudoers
    - 在文件最后添加`username（用户名） ALL=(ALL) NOPASSWD: ALL`
- passed 修改密码
- 用户与用户组
  - 可以配置多个用户、用户组
  - 一个用户可以加入多个用户组
  - linux中的权限管控可以是针对用户的也可以是针对用户组的
  - 用户组管理：只有root用户才能执行
    - groupadd 用户组名，创建用户组
    - groupdel 用户组名， 删除用户组
  - 用户管理
    - useradd  [-g -d] 用户名
      - -g指定用户的组，指定的组必须是已经存在的组，不指定会创建同名组并自动加入
        - 如-g its
      - -d指定用户的home路径，默认为/home/用户名
        - 如-d /home/test
    - userdel [-r] 用户名
      - -r 同时删除对应的home目录
    - id [用户名]，查看用户所属的组，默认查看子集
    - usermod -aG 用户组  用户名 ，将指定用户加入指定
  - getent passwd 显示操作系统中的全部用户
    - thdlrt: x:1000:1000:thdlrt:/home/thdlrt:/bin/bash
    - 用户名：密码：id：组id：描述信息：home目录：执行终端（默认bash）
  - getent group查看组的信息
    - test: x:1001:thdlrt
    - 组名：组认证：id

#### 文件权限控制

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316191457164.png" alt="image-20230316191457164" style="zoom: 60%;" />
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316191824148.png" alt="image-20230316191824148" style="zoom:50%;" />
  - 首位表示文件类型。后面每三位表示一种用户的权限
    - r：读权限（查看文件内容；ls文件夹内容）
    - w：写权限（修改文件；在文件夹内删除修改创建）
    - x：执行权限（将文件作为程序执行；cd进入文件夹，作为工作目录）
- chmod权限控制（只有文件的所属用户以及root用户才可以进修改）
  - `chmod [-R] 权限 文件、文件夹`
    - -R表示对文件夹内全部内容进行同样的操作
    - `chmod u=rwx,g=rx,o=x hello.txt`分别对三种用户的权限进行修改（只写修改的即可,并且省略-）
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316193937027.png" alt="image-20230316193937027" style="zoom:50%;" />
    - 用一位数字表示一种用户的权限状态，三个数字表示全部三种用户的权限
    - 如`chmod 751 gello.txt`
- chown修改文件拥有者/用户组
  - `chown -R [用户][:][用户组] 文件/文件夹`
    - -R对文件内全部文件执行相同的操作
    - 可选修改用户以及用户组（用:分隔）
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316194413686.png" alt="image-20230316194413686" style="zoom: 50%;" />
  - 只有root用户有权限执行这个命令

## 实用操作

#### 实用命令

- `ctrl+c`终止程序运行/放弃输入这条命令，重新换行进行输入
- `ctrl+d`退出登录（相当于exit）/退出某些程序的特定界面
- `ctrl+l`清空终端相当于clear
- `history`查看历史命令
- `!+前缀`搜索最近的匹配前缀的命令并执行
- `ctrl+r`搜索历史命令
  - 回车直接执行
  - 左右方向键获取命令
-  光标移动
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316203548007.png" alt="image-20230316203548007" style="zoom:50%;" />

### 系统时间/时区

- `date [-d] [+格式化字符串]`查看系统时间
  - -d:按照给定的字符串显示日期，一般用于日期计算
    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316215420462.png" alt="image-20230316215420462" style="zoom:50%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316215012764.png" alt="image-20230316215012764" style="zoom:50%;" />
    - 可以自由组合，如`date +%Y-%m-%d"`
- ntp时间自动校准
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316215702020.png" alt="image-20230316215702020" style="zoom:50%;" />

### 软件控制

#### 软件安装

##### yum-centos

- rpm包软件管理器，用于自动化安装配置linux软件
- `yum [-y] [install | remove | search] 软件名称`
  - -y自动确认
  - install安装
  - remove卸载
  - search搜索是否有对应的安装包
- yum需要root权限以及联网

##### apt-ubuntu

- 仅将yum换成apt

#### systemctl软件（服务）启动&关闭控制

- `systemctl start | stop |status | enable | disable 服务名`
  - 启动 | 关闭 | 查看状态 | 开启开机自启 | 关闭开机自启
- 有些第三方软件不会自动集成进来，需要手动添加

### 环境变量

- `env`查看系统中记录的环境变量，以键值对的形式进行存储
  - 可以通过`${键}`获取对应的值（大括号可以在没有歧义时省去）
- 设置
  - 临时设置`export 变量名=变量值`
  - 永久：（把export命令写入指定的文件）
    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316235914735.png" alt="image-20230316235914735" style="zoom:40%;" />
    - `source 配置文件路径`
- 把程序所在的地址/文件夹写入PATH路径可以实现在任意位置都可执行程序。
  - PATH包含了一系列不同的路径，之间用:分隔。要注意的是，添加新路径不能破坏系统原有的路径。
    - `export PATH=$PATH:自定义路径`先复制现有的PATH再添加

### 压缩解压

- 常用格式`.tar .gz`
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316221954382.png" alt="image-20230316221954382" style="zoom: 67%;" />
  - 常用
    - 压缩：`-(z)cvf name.tar（-f的压缩后的文件名称） name1.txt...(选择要压缩的文件)`
    - 解压：`-(z)xvf name,tar(要解压的文件) -C /...（指定解压目的地）`
    - -z一般在最前面，-f必须在最后面，-C单独写(不写默认为当前目录)
- zip
  - 压缩：`zip [-r] 目标名称 要压缩文件`
    - -r表示包含文件夹
  - 解压：`unzip 要解压的文件 [-d 解压目的地址]`

### 进程管理

- `ps [-e -f]`查看进程
  - -e显示全部进程；-f显示详细信息
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316214302697.png" alt="image-20230316214302697" style="zoom:50%;" />
  - 查找进程`ps -ef | grep name`
- `kill [-9] 进程ID`关闭进程
  - -9：强制关闭

### 主机状态监测

- `top`查看信息（类似任务管理器），5s刷新一次
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316233157886.png" alt="image-20230316233157886" style="zoom: 33%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316233509376.png" alt="image-20230316233509376" style="zoom: 40%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316233650098.png" alt="image-20230316233650098" style="zoom: 33%;" />
    - 重定向`top -b > test.txt`
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316234133725.png" alt="image-20230316234133725" style="zoom: 40%;" />
- 磁盘信息监控
  - `df [-h]` -h表示以更人性化的单位显示信息
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316234345059.png" alt="image-20230316234345059" style="zoom: 40%;" />
    - tps:设备每秒传输次数
- 网络监控
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316234642412.png" alt="image-20230316234642412" style="zoom: 40%;" />

### 软连接（快捷方式）

- `ln -s 参数1 参数2`
  - 参数1：被链接的（源文件）
  - 参数2：链接目的地（快捷方式）

### 网络

#### ip与主机名

- `ifconfig`查看
- `127.0.0.1`表示本机
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316210527891.png" alt="image-20230316210527891" style="zoom:35%;" />
- `hostname`查看主机名
  - `hostnamectl set-hostname 主机名`修改主机名（需要root）
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316211222362.png" alt="image-20230316211222362" style="zoom:50%;" />
  - 想要通过域名而不是IP访问服务器可以通过设置本地域名映射来实现
    - 格式`ip 主机名`
- 由于虚拟机使用DHCP获取ip，因此可能需要为虚拟机设置[固定ip](https://www.bilibili.com/video/BV1n84y1i7td?p=35&vd_source=acab52c21ffa9e9c57428e615e773279)

#### 网络传输

- ping检查是否连通
  - `ping [-c num] ip或主机名`
    - -c检查次数，省略时将无限次数持续检查，如`-c 3`检查三次
- wget下载网络文件
  - `wget [-b] url`
    - -b表示后台下载
      - 会将日志写入当前工作目录的wget-log文件
    - url下载链接
  - 中断下载`ctrl+c`
- curl发送http网络请求（下载文件、获取信息）
  - `curl [-O] url`
    - -O表示下载文件
  - 与浏览器打开网页详细，会受到网页的html的源代码
- 端口
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316213424911.png" alt="image-20230316213424911" style="zoom:50%;" />
  - `nmap ip`查看指定ip被占用的端口（需要用yum安装nmap）
  - `netstat -anp|grep 端口号`查看指定端口的占用情况

#### 上传/下载

- finalshell直接下载/拖拽（）（注意：看到文件的权限是由连接时的账号决定的，su没有用）
- 命令方式
  - 安装lrzsz
  - rz上传
    - 输入后会弹出对话框让选择要上传的文件
  - `sz 文件名`下载

## shell(补充)

- 在shell中执行命令时实际上是在执行一段 shell 可以解释执行的简短代码。对于不是shell了解的编程关键字，shell会去环境变量寻找，搜索目标程序，然后根据路径执行程序。
  - ```bash
    missing:~$ echo $PATH
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    missing:~$ which echo(获得程序位置)
    /bin/echo
    missing:~$ /bin/echo $PATH
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    ```
  
- 参数之间是不能有空格的，如`mkdir hello world`是分别创建两个文件夹

  - 要想使用带有空格的字符串参数：`\ `转义或用双引号包住字符参数

- win使用\，Mac和Linux使用/

### 常用方法

- `echo`：在 shell 中显示一行文本或字符串
  - 用途
    - `echo Hello, world!`
    - `echo $HOME`
    - 输出文件`echo "Some text" > file.txt`
    - 获取当前工作目录`pwd`
- 返回上一个目录`cd -`
- `-`后根参数名，如`--help`通常表示帮助
- mv、cp、mkdir、rm、rkdir
- 流（程序之间传递）<>
  - `echo hello > hello.txt`
  - `cat test1.txt > test2.txt`
  - \>是写入，>>表示append
  - 假设test需要管理员权限，那么`sudo echo 500 > test`**是无法运行的**：这个命令的执行过程中，`> test` 部分实际上是在当前用户的上下文中执行的，而不是在使用 `sudo` 权限的上下文中。
    - 实现上会在执行`sudo echo`之前执行`test`因此会出现问题
    - 正确做法`echo 3 | sudo tee brightness`
- 管道
  - \>和|都是重定向前一项的输出作为后一项的输入，但是>仍使用前一项作为命令的输出，而|则会使用后一项
  - `ls -l / | tail -n1`
  - 更好的阅读模式`| less`
    - `less ssh.log`
  - grep匹配
    - `-v`反向匹配
    - `-E` 参数用于启用扩展正则表达式匹配模式。
  - xargs将来自管道的**输入作为参数传递**给指定的命令。
    - `rustup toolchain list | grep nightly | grep -vE "nightly-x86" | sed 's/-x86.*//' | xargs rustup toolchain uninstall`

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

  - 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。

### Shell工具

- 脚本静态代码分析工具[shellcheck](https://github.com/koalaman/shellcheck)
  - 识别并提供关于Shell脚本中的语法错误、安全漏洞、代码风格问题和一般性问题的有用建议。
  - `shellcheck your_script.sh`

- 查看指令的使用方法`man xx`

- 查找文件find

  - ```shell
    # 查找所有名称为src的文件夹(.表示从当前目录开始查找)
    find . -name src -type d
    # 查找所有文件夹路径中包含test的python文件
    find . -path '*/test/*.py' -type f
    # 查找前一天修改的所有文件
    find . -mtime -1
    # 查找所有大小在500k至10M的tar.gz文件
    find . -size +500k -size -10M -name '*.tar.gz'
    # 删除全部扩展名为.tmp 的文件
    find . -name '*.tmp' -exec rm {} \;
    # 查找全部的 PNG 文件并将其转换为 JPG
    find . -name '*.png' -exec convert {} {}.jpg \;
    ```

  - [`fd`](https://github.com/sharkdp/fd) 就是一个更简单、更快速、更友好的程序，它可以用来作为`find`的替代品。

  - 对于频繁进行文件查找的情况 `locate` 使用一个由 [`updatedb`](https://man7.org/linux/man-pages/man1/updatedb.1.html)负责更新的数据库，在大多数系统中 `updatedb` 都会通过 [`cron`](https://man7.org/linux/man-pages/man8/cron.8.html) 每日更新。

- 查找代码（文件内容）grep

  - ```shell
    grep footbar mcd.sh
    ```

  -  `-C` ：获取查找结果的上下文（Context）；`-v` 将对结果进行反选（Invert），也就是输出不匹配的结果。举例来说， `grep -C 5` 会输出匹配结果前后五行。当需要搜索大量文件的时候，使用 `-R` 会递归地进入子目录并搜索所有的文本文件。

  -  [rg](https://github.com/BurntSushi/ripgrep)更好用

- `history`输出输入的命令的历史记录

- `tree`显示文件目录工具

### 数据处理

- `sed`基于文本编辑器`ed`构建的”流编辑器” 
  - 利用一些简短的命令来修改文件，而不是直接操作文件的内容
  - 替换文件的内容`| sed 's/.*Disconnected from //'`
    - `s/REGEX/SUBSTITUTION/`, 其中 `REGEX` 部分是我们需要使用的正则表达式，而 `SUBSTITUTION` 是用于替换匹配结果的文本。
- 统计数目
  - `wc`默认单词数目`-l`统计行

- `| sort | uniq -c`
  - `sort` 会对其输入数据进行排序。`uniq -c` 会把连续出现的行折叠为一行并使用出现次数作为前缀。

- `tail -n10`
  - `tail` 是一个用于查看文件末尾内容的命令，也可用于查看命令的输出。
  - `-n10` 选项告诉 `tail` 显示输出的最后10行内容。你可以根据需要更改数字，以显示不同数量的行。

- awk：强大的文本处理编程语言
  - `awk '{print $2}'`输出介个后的序列中 的第二个字符串
  - `| awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l`
    - 为 `awk`指定了一个匹配模式串（也就是`{...}`前面的那部分内容）。该匹配要求文本的第一部分需要等于1（这部分刚好是`uniq -c`得到的计数值），然后其第二部分必须满足给定的一个正则表达式。代码块中的内容则表示打印用户名。然后我们使用 `wc -l` 统计输出结果的行数。


- 数学计算`bc -l`
  - `| paste -sd+ | bc -l`解析输入为加法表达式，之后进行计算
  - 使用R语言进行统计`R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'`
  - 使用`gnuplot`=绘制图表
    - `gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'`

### 杂项

#### 工作控制

- 信号

  - **SIGHUP (Hangup)**：
    - **描述：** SIGHUP信号通常用于通知进程重新加载其配置或进行一些其他操作，通常由终端会话结束时触发。例如，当你**关闭一个终端会话**时，会向该会话中的进程发送SIGHUP信号，以便它们有机会执行清理工作或重新加载配置。
  - **SIGINT (Interrupt)**：
    - **描述：** SIGINT信号通常由用户按下**Ctrl+C**组合键来触发，用于终止当前运行的进程。这是一种常见的方式来停止正在运行的程序。
  - **SIGTERM (Termination)**：
    - **描述：** SIGTERM信号是用于请求进程正常终止的信号。进程可以捕获该信号并执行清理工作，然后正常退出。
  - **SIGKILL (Kill)**：
    - **描述：** SIGKILL信号是用于**强制终止进程**的信号，进程无法捕获或忽略它。使用SIGKILL会立即终止进程，而不允许进程执行清理操作。
  - 使用 `Ctrl-C` 来停止命令（将**进程stopped**）的执行时，shell 会发送一个`SIGINT` 信号到进程。

  - ```python
    #!/usr/bin/env python
    import signal, time
    # 捕获了ctrl-c发送的信号
    def handler(signum, time):
        print("\nI got a SIGINT, but I am not stopping")
    
    signal.signal(signal.SIGINT, handler)
    i = 0
    while True:
        time.sleep(.1)
        print("\r{}".format(i), end="")
        i += 1
    ```

    - 一个不会在`ctrl-c`时终止的程序
    - 可以使用`ctrl-\`发送SIGQUIT信号终止程序

  -  `Ctrl-Z` 会让 shell 发送 `SIGTSTP` 信号（会将**进程挂起为suspended**）
  - 关闭终端（ssh断开连接）会发送另外一个信号`SIGHUP`

- `jobs` 命令会列出当前终端会话中尚未完成的全部任务

  - 后台运行命令，在命令后面添加`$`后缀

  - 恢复进程`bg %1`

    - `fg`恢复到前台执行，并输出到标准输出
    - `%`加上进程的id

  - 杀死进程`kill %1 kill -SIGHUP %1`

    - 可以用于发送任何信号`kill -STOP %1`

  - ```shell
    $ sleep 1000
    ^Z
    [1]  + 18653 suspended  sleep 1000
    
    $ nohup sleep 2000 &
    [2] 18745
    appending output to nohup.out
    
    $ jobs
    [1]  + suspended  sleep 1000
    [2]  - running    nohup sleep 2000
    
    $ bg %1
    [1]  - 18653 continued  sleep 1000
    
    $ jobs
    [1]  - running    sleep 1000
    [2]  + running    nohup sleep 2000
    
    $ kill -STOP %1
    [1]  + 18653 suspended (signal)  sleep 1000
    
    $ jobs
    [1]  + suspended (signal)  sleep 1000
    [2]  - running    nohup sleep 2000
    
    $ kill -SIGHUP %1
    [1]  + 18653 hangup     sleep 1000
    
    $ jobs
    [2]  + running    nohup sleep 2000
    
    $ kill -SIGHUP %2
    
    $ jobs
    [2]  + running    nohup sleep 2000
    
    $ kill %2
    [2]  + 18745 terminated  nohup sleep 2000
    
    $ jobs
    ```

#### 终端多路复用tmux

- 大部分的命令需要先`<C-b>`激活tmux命令模式

- **会话**：每个会话都是**一个独立的工作区**，其中包含一个或多个窗口
  - `tmux` 开始一个新的会话
  - `tmux new -s NAME` 以指定名称开始一个新的会话
  - `tmux ls` 列出当前所有会话
  - 在 `tmux` 中输入 `<C-b> d` ，**将当前会话分离**
  - `tmux a` 重新连接最后一个会话。您也可以通过 `-t` 来指定具体的会话
- 窗口：相当于编辑器或是**浏览器中的标签页**，从视觉上将一个会话分割为多个部分
  - `<C-b> c` 创建一个新的窗口，使用 `<C-d>`**关闭**
  - `<C-b> N` 跳转到第 *N* 个窗口，注意每个窗口都是有编号的
  - `<C-b> p` 切换到前一个窗口
  - `<C-b> n` 切换到下一个窗口
  - `<C-b> ,` 重命名当前窗口
  - `<C-b> w` 列出当前所有窗口
- 面板：像 vim 中的**分屏**一样，面板使我们可以在一个屏幕里显示多个 shell
  - `<C-b> "` 水平分割
  - `<C-b> %` 垂直分割
  - `<C-b> <方向>` 切换到指定方向的面板，<方向> 指的是键盘上的方向键
  - `<C-b> z` 切换当前面板的缩放（临时扩展以占据窗口的全部空间）
  - `<C-b> [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分
  - `<C-b> <空格>` 在不同的面板排布间切换
  - `<C-d>`关闭一个面板

#### shell配置

- 别名alias

  - `alias alias_name="command_to_alias arg1 arg2"`

  - ```shell
    # 创建常用命令的缩写
    alias ll="ls -lh"
    
    # 能够少输入很多
    alias gs="git status"
    alias gc="git commit"
    alias v="vim"
    ```

- 配置文件dotfiles

  - 让配置（如alias）永久生效

  - 配置文件的位置

    - ```shell
      bash - ~/.bashrc, ~/.bash_profile
      git - ~/.gitconfig
      vim - ~/.vimrc 和 ~/.vim 目录
      ssh - ~/.ssh/config
      tmux - ~/.tmux.conf
      ```

  - 配置文件应该使用版本控制工具进行管理，然后通过脚本将其 **符号链接** 到需要的地方

    - **安装简单**: 如果您登录了一台新的设备，在这台设备上应用您的配置只需要几分钟的时间；
    - **可移植性**: 您的工具在任何地方都以相同的配置工作
    - **同步**: 在一处更新配置文件，可以同步到其他所有地方
    - **变更追踪**: **您可能要在整个程序员生涯中持续维护这些配置文件**，而对于长期项目而言，版本历史是非常重要的

  - 对于 `bash`来说，在大多数系统下，可以通过编辑 `.bashrc` 或 `.bash_profile` 来进行配置。在文件中可以添加需要在启动时执行的命令，例如**别名**，或者是**环境变量**。

#### 更强大的shell-zsh

- 安装并启用（以及oh my zsh）

  - ```shell
    sudo yum install zsh
    chsh -s /bin/zsh
    wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
    ```

- 

#### ssh

- 连接`ssh foo@bar.mit.edu`
  - 用户名@域名（ip）
- 远程执行命令`ssh foo@bar.mit.edu ls |grep PATTERN`
- 拷贝公钥到服务器`cat .ssh/id_ed25519.pub | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'`
  - 之后再使用ssh连接就不再需要输入密码
- 文件传输
  - 使用scp：`scp path/to/local_file foo@bar.mit.edu:path/to/remote_file`
  - `rsync `对 `scp` 进行了改进，它可以检测本地和远端的文件以防止重复拷贝。
- 在通过一个程序运行另一个程序的时候（`ssh machine foo`），**向内层的程序**（`foo`）**传递一个标志参数**。这时候你可以使用特殊参数 `--` 让某个程序 *停止处理* `--` 后面出现的标志参数以及选项（以 `-` 开头的内容）：

- `rm -- -r` 会让 `rm` 将 `-r` 当作文件名；
- `ssh machine --for-ssh -- foo --for-foo` 的 `--` 会让 `ssh` 知道 `--for-foo` 不是 `ssh` 的标志参数。

### 调试及性能分析

[调试及性能分析 · the missing semester of your cs education (missing-semester-cn.github.io)](https://missing-semester-cn.github.io/2020/debugging-profiling/)

#### 调试与日志

- （UNIX 系统）多数的程序都会将日志保存在`/var/log`下
- 

#### 性能分析	

- 

### 杂项

#### 文件结构

- `/bin` - 基本命令二进制文件
- `/sbin` - 基本的系统二进制文件，通常是root运行的
- `/dev` - 设备文件，通常是硬件设备接口文件
- `/etc` - 主机特定的**系统配置**文件
- `/home` - 系统**用户的主目录**
- `/lib` - 系统**软件通用库**
- `/opt` - 可选的应用软件
- `/sys` - 包含系统的信息和配置
- `/tmp` - 临时文件( `/var/tmp` ) 通常重启时删除
- `/usr/`**只读的用户数据**
  - `/usr/bin` - 非必须的命令二进制文件
  - `/usr/sbin` - 非必须的系统二进制文件，通常是由root运行的
  - `/usr/local/bin` - 用户编译程序的二进制文件
- `/var` -变量文件 像**日志或缓存**
