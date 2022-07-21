## 清屏

```
clear
```

 ## 文件和目录查看

 | 命令                      | 说明                                       |
 | ------------------------- | ------------------------------------------ |
 | cd /home                  | 进入 '/ home' 目录'                        |
 | cd ..                     | 返回上一级目录                             |
 | cd ../..                  | 返回上两级目录                             |
 | cd                        | 进入个人的主目录                           |
 | cd ~user1                 | 进入个人的主目录                           |
 | cd -                      | 返回上次所在的目录                         |
 | pwd                       | 显示工作路径                               |
 | ls                        | 查看目录中的文件                           |
 | ls -F                     | 查看目录中的文件                           |
 | ls -l                     | 显示文件和目录的详细资料                   |
 | ls -a                     | 显示隐藏文件                               |
 | ls *[0-9]*                | 显示包含数字的文件名和目录名               |
 | tree                      | 显示文件和目录由根目录开始的树形结构(1)    |
 | lstree                    | 显示文件和目录由根目录开始的树形结构(2)    |
 | mkdir dir1                | 创建一个叫做 'dir1' 的目录'                |
 | mkdir dir1 dir2           | 同时创建两个目录                           |
 | mkdir -p /tmp/dir1/dir2   | 创建一个目录树                             |
 | rm -f file1               | 删除一个叫做 'file1' 的文件'               |
 | rmdir dir1                | 删除一个叫做 'dir1' 的目录'                |
 | rm -rf dir1               | 删除一个叫做 'dir1' 的目录并同时删除其内容 |
 | rm -rf dir1 dir2          | 同时删除两个目录及它们的内容               |
 | mv dir1 new_dir           | 重命名/移动 一个目录                       |
 | cp file1 file2            | 复制一个文件                               |
 | cp dir/* .                | 复制一个目录下的所有文件到当前工作目录     |
 | cp -a /tmp/dir1 .         | 复制一个目录到当前工作目录                 |
 | cp -a dir1 dir2           | 复制一个目录                                |
 | ln -s file1 lnk1          | 创建一个指向文件或目录的软链接             |
 | ln file1 lnk1             | 创建一个指向文件或目录的物理链接           |
 | touch -t 0712250000 file1 | 修改一个文件或目录的时间戳 - (YYMMDDhhmm)  |
 | iconv -l                  | 列出已知的编码                             |

## YUM 软件包升级器 - （Fedora, RedHat及类似系统）

| 命令                              | 说明                                                      |
| --------------------------------- | --------------------------------------------------------- |
| yum install package_name          | 下载并安装一个rpm包                                       |
| yum localinstall package_name.rpm | 将安装一个rpm包，使用你自己的软件仓库为你解决所有依赖关系 |
| yum update package_name.rpm       | 更新当前系统中所有安装的rpm包                             |
| yum update package_name           | 更新一个rpm包                                             |
| yum remove package_name           | 删除一个rpm包                                             |
| yum list                          | 列出当前系统中安装的所有包                                |
| yum search package_name           | 在rpm仓库中搜寻软件包                                     |
| yum clean packages                | 清理rpm缓存删除下载的包                                   |
| yum clean headers                 | 删除所有头文件                                            |
| yum clean all                     | 删除所有缓存的包和头文件                                  |

## 查找指定进程格式

```
ps -ef | grep 进程关键字
# ps -A 显示进程信息
# ps -u root //显示root进程用户信息
```

## vi/vim 的使用

基本上 vi/vim 共分为三种模式，分别是**命令模式（Command mode）**，**输入模式（Insert mode）**和**底线命令模式（Last line mode）**。 这三种模式的作用分别是：

### 命令模式：

用户刚刚启动 vi/vim，便进入了命令模式。

此状态下敲击键盘动作会被Vim识别为命令，而非输入字符。比如我们此时按下i，并不会输入一个字符，i被当作了一个命令。

以下是常用的几个命令：

- **i** 切换到输入模式，以输入字符。
- **x** 删除当前光标所在处的字符。
- **:** 切换到底线命令模式，以在最底一行输入命令。

若想要编辑文本：启动Vim，进入了命令模式，按下i，切换到输入模式。

命令模式只有一些最基本的命令，因此仍要依靠底线命令模式输入更多命令。

### 输入模式

在命令模式下按下i就进入了输入模式。

在输入模式中，可以使用以下按键：

- **字符按键以及Shift组合**，输入字符
- **ENTER**，回车键，换行
- **BACK SPACE**，退格键，删除光标前一个字符
- **DEL**，删除键，删除光标后一个字符
- **方向键**，在文本中移动光标
- **HOME**/**END**，移动光标到行首/行尾
- **Page Up**/**Page Down**，上/下翻页
- **Insert**，切换光标为输入/替换模式，光标将变成竖线/下划线
- **ESC**，退出输入模式，切换到命令模式

### 底线命令模式

在命令模式下按下:（英文冒号）就进入了底线命令模式。

底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。

在底线命令模式中，基本的命令有（已经省略了冒号）：

- q 退出程序
- w 保存文件

按ESC键可随时退出底线命令模式。

简单的说，我们可以将这三个模式想成底下的图标来表示：

![img](../../图片/Linux/vim-vi-workmodel.png)

## firewalld的基本使用

 启动： systemctl start firewalld

 查看状态： systemctl status firewalld 

 停止： systemctl disable firewalld #禁止firewall开机启动

 禁用： systemctl stop firewalld #停止firewall

 2.systemctl是RHEL7&CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。

 启动一个服务：systemctl start firewalld.service

 关闭一个服务：systemctl stop firewalld.service

 重启一个服务：systemctl restart firewalld.service

 显示一个服务的状态：systemctl status firewalld.service

 在开机时启用一个服务：systemctl enable firewalld.service

 在开机时禁用一个服务：systemctl disable firewalld.service

 查看服务是否开机启动：systemctl is-enabled firewalld.service

 查看已启动的服务列表：systemctl list-unit-files|grep enabled

 查看启动失败的服务列表：systemctl --failed
