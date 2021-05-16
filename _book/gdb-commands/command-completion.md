## 命令完成

如果只有一种可能，GDB可以为您在命令中填充其余单词。它还可以随时显示命令中下一个单词的有效可能性。这适用于GDB命令，GDB子命令，命令选项以及程序中的符号名称。

每当您希望GDB填写其余单词时，请按TAB键。如果只有一种可能性，则GDB会填入单词，然后等待您完成命令（或按RET键输入）。例如，如果您键入

```
(gdb) info bre TAB
```

GDB会填充其余的“断点”一词，因为这是唯一以“ bre”开头的信息子命令：

```
(gdb) info breakpoints
```

此时，您可以按RET键以运行info breakpoints命令，或者如果'breakpoints'与您期望的命令看起来不一样，则可以按Backspace键并输入其他内容。 （如果您确定首先需要信息断点，则最好在“ info bre”后立即键入RET，以利用命令缩写而不是命令完成）。

如果在按TAB键时下一个单词存在多个可能性，则GDB会发出铃声。您可以提供更多字符，然后重试，或者再次按TAB键； GDB显示该单词的所有可能的补全。例如，您可能想在名称以“ make_”开头的子例程上设置一个断点，但是当您键入b时，make_TAB GDB会发出响声。再次键入TAB将显示程序中所有以这些字符开头的函数名称，例如：

```
(gdb) b make_ TAB
GDB sounds bell; press TAB again, to see:
make_a_section_from_file     make_environ
make_abs_section             make_function_type
make_blockvector             make_pointer_type
make_cleanup                 make_reference_type
make_command                 make_symbol_completion_list
(gdb) b make_
```

显示可用的可能性后，GDB复制您的部分输入（在示例中为“ b make_”），以便您完成命令。

如果只想首先查看替代项列表，则可以按M-？ 而不是按两次TAB键。 M-？ 表示META？。 您可以通过在键入？的同时按住键盘上指定为META shift的键（如果有）来键入此键，或者按ESC键后跟？。

如果可能的完成数量很大，GDB将打印出已收集的列表，并显示一条消息，指出该列表可能被截断。

```
(gdb) b mTABTAB
main
<... the rest of the possible completions ...>
*** List may be truncated, max-completions reached. ***
(gdb) b m
```

可以使用以下命令控制此行为：

```
set max-completions limit
set max-completions unlimited
```

设置完成候选的最大数量。一旦收集了这么多候选人，GDB将停止寻找更多的人选。当完成诸如函数名之类的事情时，这很有用，因为收集所有可能的候选者可能很耗时。默认值为200。零值将禁用制表符补全。请注意，设置无限制或非常大的限制都会使完成速度变慢。

```
show max-completions
```

显示GDB将在完成期间收集并显示的最大候选人数。

有时，您需要的字符串在逻辑上是一个“单词”，但可能包含GDB通常从其单词概念中排除的括号或其他字符。为了使单词补全在这种情况下有效，您可以在GDB命令中将单词括在'（单引号）中。

您可能需要这样做的一种可能情况是键入一个包含带有模板参数的C ++符号名称的表达式。这是因为在完成表达式时，GDB会将“ <”字符视为单词定界符，并假设它是小于比较运算符（请参阅C和C ++运算符）。

例如，当您想使用print或call命令以交互方式调用C ++模板函数时，可能需要区分是指专门用于int的name版本，name <int>（）还是专门用于float，名称为<float>（）。要在这种情况下使用单词补全功能，请在函数名称的开头键入单引号'。这会提醒GDB在您按TAB或M-键时可能需要考虑比平时更多的信息。请求单词补全：

```
(gdb) p 'func< M-?
func<int>()    func<float>()
(gdb) p 'func<
```

但是，在设置断点时（请参见指定位置），通常不需要在函数名称前输入引号，因为GDB知道您要在函数上设置断点：

```
(gdb) b func< M-?
func<int>()    func<float>()
(gdb) b func<
```

即使在键入C ++重载函数的名称（同一函数的多个定义，以参数类型区分）的情况下也是如此。 例如，当您要设置断点时，无需区分是使用int参数的name版本name（int）还是使用float参数的name版本name（float）。

```
(gdb) b bubble( M-?
bubble(int)    bubble(double)
(gdb) b bubble(dou M-?
bubble(double)
```

有关需要引用的其他方案的描述，请参见引用名称。

有关重载函数的更多信息，请参见C ++表达式。 您可以使用命令setoverload-resolution off禁用重载解析；否则，请执行以下操作。 请参阅C ++的GDB功能。

当在表达式中完成查找结构中的字段时，GDB还会尝试2将完成限制为左侧类型中可用的字段名称：

```
(gdb) p gdb_stdout.M-?
magic                to_fputs             to_rewind
to_data              to_isatty            to_write
to_delete            to_put               to_write_async_safe
to_flush             to_read
```

这是因为gdb_stdout是在GDB源中定义的struct ui_file类型的变量，如下所示：

```
struct ui_file
{
   int *magic;
   ui_file_flush_ftype *to_flush;
   ui_file_write_ftype *to_write;
   ui_file_write_async_safe_ftype *to_write_async_safe;
   ui_file_fputs_ftype *to_fputs;
   ui_file_read_ftype *to_read;
   ui_file_delete_ftype *to_delete;
   ui_file_isatty_ftype *to_isatty;
   ui_file_rewind_ftype *to_rewind;
   ui_file_put_ftype *to_put;
   void *to_data;
}
```

