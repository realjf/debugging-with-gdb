## 命令设置

许多命令根据特定于命令的变量或设置更改其行为。 可以使用set子命令来更改这些设置。 例如，打印命令（请参阅检查数据）根据可通过设置设置打印元素NUMBER-OF-ELEMENTS和设置打印数组索引等命令更改的设置来不同地打印数组。

您可以在GDB启动时加载的gdbinit文件中根据自己的喜好更改这些设置。 请参阅启动。

也可以在调试会话期间以交互方式更改设置。 例如，要更改要打印的数组元素的限制，可以执行以下操作：

```
(GDB) set print elements 10
(GDB) print some_array
$1 = {0, 10, 20, 30, 40, 50, 60, 70, 80, 90...}
```

上面设置的print elements 10命令将要打印的元素数从默认值200更改为10。如果仅打算将此限制10用于打印some_array，则必须使用设置的打印元素200将限制恢复回200。

某些命令允许使用命令选项覆盖设置。 例如，print命令支持许多选项，这些选项允许覆盖由set print子命令设置的相关全局打印设置。 请参阅打印选项。 上面的示例可以重写为：

```
(GDB) print -elements 10 -- some_array
$1 = {0, 10, 20, 30, 40, 50, 60, 70, 80, 90...}
```

或者，您可以在命令调用期间使用with命令临时更改设置。

```
with setting [value] [-- command]
w setting [value] [-- command]
```

在命令持续时间内将设置临时设置为值。

设置是您可以使用set子命令更改的任何设置。 value是运行命令时要分配给设置的值。

如果未提供命令，则重复执行最后执行的命令。

如果提供了命令，则必须在命令前加上双破折号（-）。 这是必需的，因为某些设置接受自由格式的参数，例如表达式或文件名。

例如，命令

```
(GDB) with print array on -- print some_array
```

等效于以下3个命令：

```
(GDB) set print array on
(GDB) print some_array
(GDB) set print array off
```

当您要在运行用户定义的命令或Python或Guile中定义的命令时覆盖设置时，with命令特别有用。请参阅扩展GDB。

```
(GDB) with print pretty on -- my_complex_command
```

要更改同一命令的多个设置，可以嵌套命令。例如，如果使用语言ada-使用打印元素10，则暂时将语言更改为Ada，并将数组和字符串的打印限制设置为10个。

