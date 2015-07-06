---
layout: default
title: shell学习之 find用法
category: 技术文档
comments: true
---

**shell学习之 find用法**

 


----------


 find base_path
base_path可以是任何位置(/home/bigollo),find会从给位置开始向下查找。
 例如：
 $find . -print
 打印文件和目录的列表
 . 指定当前目录，..指定父目录。这是UNIX文件系统中的约定用法。
 -print 指明打印出匹配文件的文件名(路径)。当使用-print时，'\n'作为用于分隔文件的定界符。
 -print0 指明使用'\0'作为定界符来打印每一个匹配的文件名。当文件名中包含换行符时，这个方法就有用武之地了。
 1.根据文件名或正则表达式匹配搜索
 选项-name的参数制定了文件名所必须匹配的字符串。我们可以通过通配符作为参数使用。*.txt能够匹配所有已.txt结尾的文件名。选项-print 在终端中打印出符合条件(例如 -name)的文件名或文件路径，这些匹配条件作为find命令的选项给出。
find 命令有一个选项 -iname(忽略字母大小写)，该选项的作用和-name作用类似，只不过匹配名字的时候会忽略大小写。
例如：
$ls
example.txt EXAMPLUE.txt fine.txt

$find . -iname "example*" -print

./examaple.txt
./EXAMPLUE.txt
如何想匹配多个条件中的一个，可以采用OR条件操作：
$ls
new.txt some.jpg text.pdf

$find .\( -name "*.txt" -o -name "*.pdf" \) -print
./text.pdf
./new.txt
上面的代码会打印出所有的.txt和.pdf文件，是因为这个find命令能够 匹配所有这两类文件。\(以及\)用于将-name "*.txt" -o -name "*.pdf"视为一个整体。
选项-path的参数可以使用通配符来匹配文件路径或文件。-name 总是用给定的文件名进行匹配。-path则将文件路径作为一个整体进行匹配。例如：
$find /home/users -path "*slynux*" -print
This will match files as following paths.
/home/users/list/slynux.txt
/home/users/slynux/eg.css
选项 -regex 的参数和-path的类似，只不过 -regex是基于正则表达式来匹配文件路径的。并打印文本。使用正则表达式进行文本匹配的一个经典例子就是从一堆文本中解析出所有的e-mail地址。一个e-mail地址通常采用name@host.root这种形式，所以，可以将其一般化为[a-z0-9]+@[a-z0-9]+.[a-z0-9]+。+指明在他之前的字符类中的字符可以出现一次或者多次。
	下面命令匹配.py或者.sh文件：
$ls
new.py next.jpg test.py

$find . -regex ".*\(\.py\|\.sh)$"
./test.py
类似的，-iregex用于忽略正则表达式的大小写。
2，否定参数
find 也可以用"!" 否定参数的含义。例如：
$find . ! -name "*.txt" -print

上面的find命令能够匹配所有不以.txt结尾的文件名。下面就是这个命令的运行结果：
$ls
list.txt  new.py   new.txt   next.jpg   test.py

$find . ! -name "*.txt" -print
.
./next.jpg
./test.py
./new.py
3,基于目录深度的搜索
find 命令在使用时会遍历所有的子目录。我们可以采用一些深度参数来限制find命令遍历的深度。-maxdepth和-mindepth就是这类参数。

$find . -maxdepth 1 -type f -print

该命令只列出当前目录下的所有普通文件。

$find . -mindepth 2 -type f -print

./dir1/dir2/file1
./dir3/dir4/f2

即使当前目录或dir1和dir3中包含有文件，它们也不会打印出来。
4.根据文件类型搜索
-type 可以对文件搜索进行过滤。借助这个选项，我们可以为find命令指明特定的文件匹配类型。
只列出所有的目录：
$find . -type d -print

将文件和目录分别列出来可不是个容易事。不过有了find就好办了。例如，只列出普通文件：
$find . -type f -print

只列出符号链接：
$find . -type l -print

5.根据文件时间进行搜索
unix/linux文件系统中的每个文件都有三种时间戳(timestamp),如下所示。

 - 访问时间(-atime):用户最近一次访问文件的时间。
 - 修改时间(-mtime):文件内容最后一次被修改的时间。
 - 变化时间(-ctime):文件元数据(metadata,例如权限和所有权)最后一次改变时间。

在UNIX中并没有所谓“创建时间”的概念。
-atime,-mtime,-ctime可作为find的时间参数。它们可以整数值给出，单位是天。这些整数值通常还带有-或+：-表示小于，+表示大于，如下所示。
打印出在最近七天内被访问过的所有文件：
$find . -type f -atime -7 -print

打印出恰好在七天前被访问过的所有文件：
$find . -type f -atime 7 -print

打印出访问时间超过7天的所有文件
$find . -type f -atime +7 -print
