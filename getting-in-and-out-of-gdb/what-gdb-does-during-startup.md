## GDB在启动期间会做什么

这是有关GDB在会话启动过程中执行的操作的描述：

1. 设置命令行指定的命令解释器（请参阅解释器）。

2. 读取系统范围的初始化文件（如果在构建GDB时使用了--with-system-gdbinit；请参阅系统范围的配置和设置）和系统范围的gdbinit目录中的文件（如果--with-system-gdbinit-使用dir），并执行这些文件中的所有命令。这些文件需要使用.gdb扩展名来命名，才能解释为GDB命令，或者可以使用受支持的脚本语言以及相应的文件扩展名来编写这些文件。

3. 读取主目录1中的初始化文件（如果有），并执行该文件中的所有命令。

4. 按指定顺序执行由“ -iex”和“ -ix”选项指定的命令和命令文件。通常，您应该改用“ -ex”和“ -x”选项，但是通过这种方式，您可以在执行GDB初始化文件之前和在加载劣等文件之前应用设置。

5. 处理命令行选项和操作数。

6. 只要将“ set auto-load local-gdbinit”设置为“ on”，就从当前工作目录中的初始化文件（如果有）读取并执行命令（请参阅当前目录中的初始化文件）。仅当当前目录与主目录不同时，才执行此操作。因此，您可以在调用GDB的目录中拥有多个初始化文件，一个在主目录中，一个是通用文件，另一个在调试目录中，特定于您要调试的程序。

7. 如果命令行指定了要调试的程序，要附加的过程或核心文件，则GDB会加载为该程序或其加载的共享库提供的所有自动加载的脚本。请参阅自动加载。
   如果希望在启动过程中禁用自动加载，则必须执行以下操作：

   $ gdb -iex“关闭自动加载python脚本” myprogram
   选项“ -ex”不起作用，因为随后自动加载已关闭。

8. 按指定顺序执行由“ -ex”和“ -x”选项指定的命令和命令文件。有关GDB命令文件的更多详细信息，请参见命令文件。
   读取历史文件中记录的命令历史。有关命令历史记录和GDB记录命令的文件的更多详细信息，请参见命令历史记录。

9. 初始化文件使用与命令文件相同的语法（请参阅命令文件），并且由GDB以相同的方式进行处理。您主目录中的init文件可以设置选项（例如“设置投诉”），这些选项会影响命令行选项和操作数的后续处理。如果您使用“ -nx”选项，则不执行初始化文件（请参阅选择模式）。

要显示gdb在启动时加载的init文件的列表，可以使用gdb --help。

GDB初始化文件通常称为.gdbinit。由于DOS文件系统施加的文件名的限制，GDB的DJGPP端口使用名称gdb.ini。 GDB的Windows端口使用标准名称，但是如果它在您的主目录中找到gdb.ini文件，它会警告您，并建议将文件重命名为标准名称。