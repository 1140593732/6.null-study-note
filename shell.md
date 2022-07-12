# shell

## 1.使用shell 

​		打开终端时，看到如下提示符：`missing`是主机名，`~`表示当前位置在home，`$`符号表示当前身份不是root用户。

```
missing:~$
```

​		执行命令（执行程序`date`，打印出当前的日期和时间）：

```
missing:~$ date
Fri 10 Jan 2020 11:49:31 AM EST
missing:~$
```

​		执行程序`echo`，同时指定参数`hello`：

```
missing:~$ echo hello
hello
```

​		 shell 基于空格分割命令并进行解析，然后执行第一个单词代表的程序，并将后续的单词作为程序可以访问的参数。如果传递的参数当中包含空格，则需要使用单引号，双引号将其包裹起来，或者使用转义符号 `\` ，例如：（"My Photos"） 或者（My\ Photos）。

## 2.在shell中导航

​		shell 中的路径是一组被分割的目录，在 Linux 和 macOS 上使用 `/` 分割，而在Windows上是 `\`。路径 `/` 代表的是系统的根目录，所有的文件夹都包括在这个路径之下，在Windows上每个盘都有一个根目录（例如： `C:\`）。 我们假设您在学习本课程时使用的是 Linux 文件系统。如果某个路径以 `/` 开头，那么它是一个 *绝对路径*，其他的都是 *相对路径* 。相对路径是指相对于当前工作目录的路径，当前工作目录可以使用 `pwd` 命令来获取。此外，切换目录需要使用 `cd` 命令。在路径中，`.` 表示的是当前目录，而 `..` 表示上级目录：

```
missing:~$ pwd
/home/missing
missing:~$ cd /home
missing:/home$ pwd
/home
missing:/home$ cd ..
missing:/$ pwd
/
missing:/$ cd ./home
missing:/home$ pwd
/home
missing:/home$ cd missing
missing:~$ pwd
/home/missing
missing:~$ ../../bin/echo hello
hello
```

​		通常，我们运行一个程序时，若没有指定路径，则该程序会在当前目录下执行。例如，我们常常会搜索文件，并在需要时创建文件。

​		为了查看指定目录下包含哪些文件，我们使用 `ls` 命令：

```
missing:~$ ls
missing:~$ cd ..
missing:/home$ ls
missing
missing:/home$ cd ..
missing:/$ ls
bin
boot
dev
etc
home
...
```

​		除非我们利用第一个参数指定目录，否则 `ls` 会打印当前目录下的文件。

​		大多数的命令接受标记和选项（带有值的标记），它们以 `-` 开头，并可以改变程序的行为。通常，在执行程序时使用 `-h` 或 `--help` 标记可以打印帮助信息，以便了解有哪些可用的标记或选项。例如，当我们使用`ls --help`时，输出中的某一行如下：

```
  -l                         use a long listing format
```

```
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

​		运行后的结果行中，字符`d`代表`missing`是一个目录。接下来的九个字符，每三个字符构成一组。 （`rwx`）. 它们分别代表了文件所有者（`missing`），用户组（`users`） 以及其他所有人具有的权限。其中 `-` 表示该用户不具备相应的权限。从上面的信息来看，只有文件所有者可以修改（`w`），`missing` 文件夹 （例如，添加或删除文件夹中的文件）。为了进入某个文件夹，用户需要具备该文件夹以及其父文件夹的“搜索”权限（以“可执行”：`x`）权限表示。为了列出它的包含的内容，用户必须对该文件夹具备读权限（`r`）。对于文件来说，权限的意义也是类似的。注意，`/bin` 目录下的程序在最后一组，即表示所有人的用户组中，均包含 `x` 权限，也就是说任何人都可以执行这些程序。

​		此外还有一些命令需要掌握，例如：`mv`（用于重命名或者移动文件）、`cp`（拷贝文件）以及`mkdir`（新建文件夹）。

```
[guoanyi@localhost home]$ mv dotfiles.md foo.md             #将文件"dotfiles.md"重命名为"food.md"
[guoanyi@localhost home]$ cp dotfiles.md ../food.md         #将文件"dotfiles.md"复制到../food.md
[guoanyi@localhost home]$ rm ../food.md                     #删除../food.md文件
```

​		Linux上，默认情况下删除一般是非递归的，不能使用`rm`删除目录，可以通过发送`-r`标志来允许进行递归删除，例如：

```
                          rm -rf 目录名字                    # -r 向下递归，不管有多少级目录，一并删除
                                                            # -f 直接强行删除，没有任何提示
```

​		可以使用`rmdir`来删除目录，但只允许目录为空时才能进行删除。

​		可以使用`mkdir`来创建一个目录。

​		此外，如果想要知道关于程序参数、输入输出的信息，亦或是想要了解它们的工作方式，可以使用`man` 这个程序。它会接受一个程序名作为参数，然后将它的文档（用户手册）展现出来。注意，使用 `q` 可以退出该程序（或者使用`ctrl l`来清除终端并返回顶部）。

```
missing:~$ man ls
```

## 3.在程序间创建连接

​		在 shell 中，程序有两个主要的“流”：它们的输入流和输出流。 当程序尝试读取信息时，它们会从输入流中进行读取，当程序打印信息时，它们会将信息输出到输出流中。 通常，一个程序的输入输出流都是您的终端。也就是，您的键盘作为输入，显示器作为输出。 但是，我们也可以重定向这些流！

​		最简单的重定向是 `< file` 和 `> file`。这两个命令可以将程序的输入输出流分别重定向到文件：

```
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello
missing:~$ cat < hello.txt
hello
missing:~$ cat < hello.txt > hello2.txt       #将hello.txt作为cat的输入，并将cat输出的所有内容写入hello2.txt
missing:~$ cat hello2.txt
hello
```

 		还可以使用 `>>` 来向一个文件追加内容（`>`是覆盖其中的内容，而`>>`则是在末尾添加内容）。

​		使用管道（ *pipes* ），我们能够更好的利用文件重定向。 `|` 操作符允许我们将一个程序的输出和另外一个程序的输入连接起来：

```
missing:~$ ls -l / | tail -n1                                     #输出"ls -l /"打印出的内容的最后一行
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var

missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2
219
```

## 4.一个功能全面又强大的工具

​		对于大多数的类 Unix 系统，有一类用户是非常特殊的，那就是：根用户（root user）。 您应该已经注意到了，在上面的输出结果中，根用户几乎不受任何限制，他可以创建、读取、更新和删除系统中的任何文件。 通常在我们并不会以根用户的身份直接登录系统，因为这样可能会因为某些错误的操作而破坏系统。 取而代之的是我们会在需要的时候使用 `sudo` 命令。顾名思义，它的作用是让您可以以 su（super user 或 root 的简写）的身份执行一些操作。 当您遇到拒绝访问（permission denied）的错误时，通常是因为此时您必须是根用户才能操作。然而，请再次确认您是真的要执行此操作（通过`sudo su`切换到根用户）。

​		有一件事情是您必须作为根用户才能做的，那就是向 `sysfs` 文件写入内容。系统被挂载在 `/sys` 下，`sysfs` 文件则暴露了一些内核（kernel）参数。 因此，您不需要借助任何专用的工具，就可以轻松地在运行期间配置系统内核。**注意 Windows 和 macOS 没有这个文件**。

​		例如，您笔记本电脑的屏幕亮度写在 `brightness` 文件中，它位于

```
/sys/class/backlight
```

​		通过将数值写入该文件，我们可以改变屏幕的亮度。现在，蹦到您脑袋里的第一个想法可能是：

```
$ sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'
/sys/class/backlight/thinkpad_screen/brightness
$ cd /sys/class/backlight/thinkpad_screen
$ sudo echo 3 > brightness
An error occurred while redirecting file 'brightness'
open: Permission denied
```

​		出乎意料的是，我们还是得到了一个错误信息。毕竟，我们已经使用了 `sudo` 命令！关于 shell，有件事我们必须要知道。`|`、`>`、和 `<` 是通过 shell 执行的，而不是被各个程序单独执行。 `echo` 等程序并不知道 `|` 的存在，它们只知道从自己的输入输出流中进行读写。 对于上面这种情况， *shell* (权限为您的当前用户) 在设置 `sudo echo` 前尝试打开 brightness 文件并写入，但是系统拒绝了 shell 的操作因为此时 shell 不是根用户。

​		明白这一点后，我们可以这样操作：

```
$ echo 3 | sudo tee brightness
```

​	因为打开 `/sys` 文件的是 `tee` 这个程序，并且该程序以 `root` 权限在运行，因此操作可以进行。 这样您就可以在 `/sys` 中愉快地玩耍了，例如修改系统中各种LED的状态（路径可能会有所不同）：

```
$ echo 1 | sudo tee /sys/class/leds/input6::scrolllock/brightness
```

