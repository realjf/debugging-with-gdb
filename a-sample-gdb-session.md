## 1 一个gdb会话示例

您可以在闲暇时使用本手册来阅读有关GDB的全部信息。但是，只有少数几个命令足以开始使用调试器。本章说明了这些命令。

GNU m4（通用宏处理器）的初步版本之一显示以下错误：有时，当我们从默认值更改其引号字符串时，用于捕获另一个宏定义中的命令的命令将停止工作。 在接下来的m4简短会话中，我们定义了一个宏foo，它扩展为0000； 然后，我们使用m4内置的defn将bar定义为同一对象。 但是，当我们将打开引号字符串更改为<QUOTE>并将关闭引号字符串更改为<UNQUOTE>时，相同的过程无法定义新的同义词baz：

```
$ cd gnu/m4
$ ./m4
define(foo,0000)

foo
0000
define(bar,defn(‘foo’))

bar
0000
changequote(<QUOTE>,<UNQUOTE>)

define(baz,defn(<QUOTE>foo<UNQUOTE>))
baz
Ctrl-d
m4: End of input: 0: fatal error: EOF in string
```

让我们使用GDB尝试看看发生了什么。

```
$ gdb m4
GDB is free software and you are welcome to distribute copies
 of it under certain conditions; type "show copying" to see
 the conditions.
There is absolutely no warranty for GDB; type "show warranty"
 for details.

GDB 10.2.90.20210515-git, Copyright 1999 Free Software Foundation, Inc...
(gdb)
```

GDB仅读取足够的符号数据，以在需要时知道在哪里可以找到其余的数据。结果，第一个提示很快出现。现在，我们告诉GDB使用比通常更窄的显示宽度，以便使示例适合本手册。

```
(gdb) set width 70
```

我们需要查看m4内置的changequote的工作原理。看了源代码之后，我们知道相关的子例程是m4_changequote，因此我们在其中使用GDB break命令设置了一个断点。

```
(gdb) break m4_changequote
Breakpoint 1 at 0x62f4: file builtin.c, line 879.
```

使用run命令，我们在GDB的控制下启动m4；只要控件未到达m4_changequote子例程，该程序将照常运行：

```
(gdb) run
Starting program: /work/Editorial/gdb/gnu/m4/m4
define(foo,0000)

foo
0000
```

为了触发断点，我们调用changequote。 GDB暂停m4的执行，显示有关它停止的上下文的信息。

```
changequote(<QUOTE>,<UNQUOTE>)

Breakpoint 1, m4_changequote (argc=3, argv=0x33c70)
    at builtin.c:879
879         if (bad_argc(TOKEN_DATA_TEXT(argv[0]),argc,1,3))
```

现在，我们使用命令n（next）将执行推进到当前函数的下一行。

```
(gdb) n
882         set_quotes((argc >= 2) ? TOKEN_DATA_TEXT(argv[1])\
 : nil,
```

set_quotes看起来像一个很有前途的子例程。我们可以通过使用命令s（step）而不是下一步进入它。步骤转到要在任何子例程中执行的下一行，因此它进入set_quotes。

```
(gdb) s
set_quotes (lq=0x34c78 "<QUOTE>", rq=0x34c88 "<UNQUOTE>")
    at input.c:530
530         if (lquote != def_lquote)
```

显示m4现在被挂起的子例程（及其参数）的显示称为堆栈帧显示。 它显示了堆栈的摘要。 我们可以使用backtrace命令（也可以将其拼写为bt）来查看我们在整个堆栈中的位置：backtrace命令显示每个活动子例程的堆栈帧。

```
(gdb) bt
#0  set_quotes (lq=0x34c78 "<QUOTE>", rq=0x34c88 "<UNQUOTE>")
    at input.c:530
#1  0x6344 in m4_changequote (argc=3, argv=0x33c70)
    at builtin.c:882
#2  0x8174 in expand_macro (sym=0x33320) at macro.c:242
#3  0x7a88 in expand_token (obs=0x0, t=209696, td=0xf7fffa30)
    at macro.c:71
#4  0x79dc in expand_input () at macro.c:40
#5  0x2930 in main (argc=0, argv=0xf7fffb20) at m4.c:195
```

我们再经过几行，看看会发生什么。前两次，我们可以使用“ s”；接下来的两次，我们使用n避免陷入xstrdup子例程中

```
(gdb) s
0x3b5c  532         if (rquote != def_rquote)
(gdb) s
0x3b80  535         lquote = (lq == nil || *lq == '\0') ?  \
def_lquote : xstrdup(lq);
(gdb) n
536         rquote = (rq == nil || *rq == '\0') ? def_rquote\
 : xstrdup(rq);
(gdb) n
538         len_lquote = strlen(rquote);
```

显示的最后一行看起来有些奇怪。我们可以检查变量lquote和rquote，看看它们是否实际上是我们指定的新的左引号和右引号。我们使用命令p（print）查看它们的值。

```
(gdb) p lquote
$1 = 0x35d40 "<QUOTE>"
(gdb) p rquote
$2 = 0x35d50 "<UNQUOTE>"
```

lquote和rquote确实是新的左右引号。为了查看某些上下文，我们可以使用l（list）命令在当前行周围显示十行源代码。

```
(gdb) l
533             xfree(rquote);
534
535         lquote = (lq == nil || *lq == '\0') ? def_lquote\
 : xstrdup (lq);
536         rquote = (rq == nil || *rq == '\0') ? def_rquote\
 : xstrdup (rq);
537
538         len_lquote = strlen(rquote);
539         len_rquote = strlen(lquote);
540     }
541
542     void
```

让我们越过设置len_lquote和len_rquote的两行，然后检查这些变量的值

```
(gdb) n
539         len_rquote = strlen(lquote);
(gdb) n
540     }
(gdb) p len_lquote
$3 = 9
(gdb) p len_rquote
$4 = 7
```

假设len_lquote和len_rquote分别是lquote和rquote的长度，那肯定看起来是错误的。 我们可以使用p命令将它们设置为更好的值，因为它可以打印任何表达式的值，并且该表达式可以包含子例程调用和赋值。

```
(gdb) p len_lquote=strlen(lquote)
$5 = 7
(gdb) p len_rquote=strlen(rquote)
$6 = 9
```

这足以解决在m4内置defn中使用新引号的问题吗？我们可以让m4继续执行c（continue）命令，然后尝试最初引起麻烦的示例：

```
(gdb) c
Continuing.

define(baz,defn(<QUOTE>foo<UNQUOTE>))

baz
0000
```

成功！现在，新报价与默认报价一样有效。问题似乎只是两种定义错误长度的错别字。我们通过给它一个EOF作为输入来允许m4退出：

```
Ctrl-d
Program exited normally.
```

消息“程序正常退出。”来自GDB；它指示m4已完成执行。我们可以使用GDB quit命令结束GDB会话。

