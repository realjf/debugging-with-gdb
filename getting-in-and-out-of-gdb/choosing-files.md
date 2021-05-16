## 选择文件

GDB启动时，它将读取除选项以外的所有参数，以指定可执行文件和核心文件（或进程ID）。这与分别由“ -se”和“ -c”（或“ -p”）选项指定的参数相同。 （GDB读取不具有与'-se'选项等效的关联选项标志的第一个参数，后跟该参数；以及不具有关联选项标志（如果有的话）的第二个参数，其等效于'- c'/'-p'选项后跟该参数。）如果第二个参数以十进制数字开头，则GDB将首先尝试将其附加为进程，如果失败，则尝试将其作为核心文件打开。如果您有一个名称以数字开头的corefile，则可以通过以./作为前缀来防止GDB将其视为pid。 ./12345。

如果尚未将GDB配置为包括核心文件支持（例如，对于大多数嵌入式目标），则它将抱怨第二个参数并忽略它。

许多选择都有长短形式。两者都显示在下面的列表中。如果您截断了长格式，GDB也会识别出长格式，只要存在足够多的选项即可。 （尽管您愿意，可以用'-'而不是'-'标记选项参数，尽管我们举例说明了更常见的约定。）

```
-symbols file
-s file
```

从文件文件中读取符号表。

```
-exec file
-e file
```

使用文件文件作为可执行文件在适当时执行，并与核心转储一起检查纯数据。

```
-se file
```

从文件文件中读取符号表，并将其用作可执行文件。

```
-core file
-c file
```

使用文件文件作为核心转储进行检查。

```
-pid number
-p number
```

与attach命令一样，连接到进程ID号。

```
-command file
-x file
```

从文件文件执行命令。该文件的内容的评估与source命令的评估完全相同。请参阅命令文件。

```
-eval-command command
-ex command
```

执行一个GDB命令。 

可以多次使用此选项来调用多个命令。根据需要，它也可以与“ -command”交错。

```
gdb -ex 'target sim' -ex 'load' \
   -x setbreakpoints -ex 'run' a.out
```

```
-init-command file
-ix file
```

在加载劣质文件之前（但在加载gdbinit文件之后）从文件文件执行命令。请参阅启动。

```
-init-eval-command command
-iex command
```

在加载下级之前（但在加载gdbinit文件之后）执行一个GDB命令。请参阅启动。

```
-directory directory
-d directory
```

将目录添加到路径中以搜索源文件和脚本文件。

```
-r
-readnow
```

立即读取每个符号文件的整个符号表，而不是默认值，默认情况下是按需要增量读取它。这使启动速度变慢，但使以后的操作变快。

```
--readnever
```

不要读取每个符号文件的符号调试信息。 这使启动速度更快，但以无法执行符号调试为代价。 也不会读取DWARF展开信息，这意味着回溯可能变得不完整或不准确。 这种用法的一种用法是，当用户只想执行以下顺序时：附加，转储核心，分离。 在这种情况下，加载调试信息是不必要的延迟原因。
