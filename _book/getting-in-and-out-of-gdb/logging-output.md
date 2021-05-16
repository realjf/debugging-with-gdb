## 日志记录输出

您可能需要将GDB命令的输出保存到文件中。有几个命令可以控制GDB的日志记录。

```
set logging on
```

启用日志记录

```
set logging off
```

关闭日志记录

```
set logging file file
```

更改当前日志文件的名称。默认日志文件是gdb.txt。

```
set logging overwrite [on|off]
```

默认情况下，GDB将追加到日志文件。如果要设置登录以覆盖日志文件，则设置覆盖。

```
set logging redirect [on|off]
```

默认情况下，GDB输出将同时到达终端和日志文件。如果只希望输出到日志文件，则设置重定向。

```
set logging debugredirect [on|off]
```

默认情况下，GDB调试输出将同时到达终端和日志文件。如果希望调试输出仅进入日志文件，则设置debugredirect。

```
show logging
```

显示日志记录设置的当前值。

您也可以将GDB命令的输出重定向到shell命令。