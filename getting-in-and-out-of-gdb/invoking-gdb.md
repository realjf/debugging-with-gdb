## 调用gdb

通过运行程序gdb来调用GDB。 一旦启动，GDB将从终端读取命令，直到您告诉它退出。

您还可以使用各种参数和选项运行gdb，以在一开始就指定更多的调试环境。

此处描述的命令行选项旨在涵盖各种情况。 在某些环境中，其中某些选项可能实际上不可用。

启动GDB的最常用方法是使用一个参数指定一个可执行程序：

```
gdb program
```

您还可以从可执行程序和指定的核心文件开始：

```
gdb program core
```

如果要调试正在运行的进程，可以改为将进程ID指定为第二个参数，或使用选项-p：

```
gdb program 1234
gdb -p 1234
```

会将GDB附加到进程1234。使用选项-p，您可以省略程序文件名。

利用第二个命令行参数需要一个相当完整的操作系统。 当您将GDB用作连接到裸板上的远程调试器时，可能没有“进程”的概念，并且通常没有办法获得核心转储。 如果GDB无法附加或读取核心转储，它将警告您。

您可以选择使gdb使用--args将可执行文件后的所有参数传递给下级。 此选项停止选项处理。

```
gdb --args gcc -O2 -c foo.c
```

这将导致gdb调试gcc，并将gcc的命令行参数（请参阅参数）设置为-O2 -cfoo.c。

您可以通过指定--silent（或-q /-quiet）来运行gdb，而无需打印用于描述GDB非保修的前端材料。

```
gdb --silent
```

您可以使用命令行选项进一步控制GDB的启动方式。 GDB本身可以提醒您可用的选项。

```
gdb -help
```

显示所有可用选项并简要描述它们的用法（“ gdb -h”是较短的等效项）。

您提供的所有选项和命令行参数将按顺序处理。使用“ -x”选项时，顺序会有所不同。
