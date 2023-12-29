# linux

## 基础

### [vim](vim.md)

### 基础概念

- 命令格式 command [-options] [parameter]
  - command 命令本身
  - -options 命令的一些选项，控制行为细节
    - 可以组合使用，如：-l -a 或-la 或-al 均可
  - parameter 命令的参数，多用于指向目标
- linux 系统终端默认会加载 HOME 目录为当前的工作目录
  - HOME 目录：每个 Linux 系统的个人账户目录（/home/用户名）
  - /home/用户名/~

- 通配符
  - \* 匹配任意内容
  - test\* 以 test 开头
  - \*test 以 test 结尾
  - \*test\* 包含 test
- | 管道符，将管道左边命令的结果作为右边命令的输入
  - 比如 cat text 01. txt | grep xxx 等价于 grep xxx text 01. txt
  - 如查找文件 ls xx | grep xx
  - 嵌套使用 xx|xx|xx|... 依次向右传递

- 查看指令的使用方法 `man xx
- `history` 输出输入的命令的历史记录
- `tree` 显示文件目录工具 `

### [[文件系统]]

### [[权限管理]]

### [[进程管理]]

## 杂项

### 软件安装

#### yum-centos

- rpm包软件管理器，用于自动化安装配置linux软件
- `yum [-y] [install | remove | search] 软件名称`
  - -y自动确认
  - install安装
  - remove卸载
  - search搜索是否有对应的安装包
- yum需要root权限以及联网
#### apt-ubuntu

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

## shell(补充)

- 在shell中执行命令时实际上是在执行一段 shell 可以解释执行的简短代码。对于不是shell了解的编程关键字，shell会去环境变量寻找，搜索目标程序，然后根据路径执行程序。


```bash
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

### [[Shell编程]]

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
```shell
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
```shell
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


```shell
sudo yum install zsh
chsh -s /bin/zsh
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

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

## 杂项

### 快捷键

- `ctrl+c` 终止程序运行/放弃输入这条命令，重新换行进行输入
- `ctrl+d` 退出登录（相当于 exit）/退出某些程序的特定界面
- `ctrl+l` 清空终端相当于 clear
- `history` 查看历史命令
- `!+前缀` 搜索最近的匹配前缀的命令并执行
- `ctrl+r` 搜索历史命令
  - 回车直接执行
  - 左右方向键获取命令
-  光标移动
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316203548007.png" alt="image-20230316203548007" style="zoom:50%;" />

### 系统时间/时区

- `date [-d] [+格式化字符串]` 查看系统时间
  - -d: 按照给定的字符串显示日期，一般用于日期计算
    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316215420462.png" alt="image-20230316215420462" style="zoom:50%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316215012764.png" alt="image-20230316215012764" style="zoom:50%;" />
    - 可以自由组合，如 `date +%Y-%m-%d"`
- ntp 时间自动校准
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316215702020.png" alt="image-20230316215702020" style="zoom:50%;" />