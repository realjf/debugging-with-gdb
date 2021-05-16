## shell命令

如果您需要在调试会话期间偶尔执行shell命令，则无需离开或挂起GDB。您可以只使用shell命令。

```
shell command-string
!command-string
```

调用标准外壳程序以执行命令字符串。 请注意，！之间不需要空格。 和命令字符串。 在GNU和Unix系统上，环境变量SHELL（如果存在）确定要运行的shell。 否则，GDB使用默认外壳程序（在GNU和Unix系统上为/ bin / sh，在MS-Windows上为cmd.exe，在MS-DOS上为COMMAND.COM，等等）。

在开发环境中经常需要实用工具make。在GDB中，您不必为此使用shell命令：

```
make make-args
```

使用指定的参数执行make程序。这等效于“ shell make-args”。

```
pipe [command] | shell_command
| [command] | shell_command
pipe -d delim command delim shell_command
| -d delim command delim shell_command
```

执行命令并将其输出发送到shell_command。请注意，在|周围不需要空格。如果未提供命令，则重复执行最后执行的命令。

如果命令包含|，则选项-d delim可用于指定将命令与shell_command分开的备用定界符字符串delim。

实例：

```
(gdb) p var
$1 = {
  black = 144,
  red = 233,
  green = 377,
  blue = 610,
  white = 987
}
(gdb) pipe p var|wc
      7      19      80
(gdb) |p var|wc -l
7
(gdb) p /x var
$4 = {
  black = 0x90,
  red = 0xe9,
  green = 0x179,
  blue = 0x262,
  white = 0x3db
}
(gdb) ||grep red
  red => 0xe9,
(gdb) | -d ! echo this contains a | char\n ! sed -e 's/|/PIPE/'
this contains a PIPE char
(gdb) | -d xxx echo this contains a | char!\n xxx sed -e 's/|/PIPE/'
this contains a PIPE char!
(gdb)
```

方便变量$ _shell_exitcode和$ _shell_exitsignal可用于检查由shell，make，pipe和|启动的最后一个shell命令的退出状态。

