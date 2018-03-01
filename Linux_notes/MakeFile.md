Makefile
--------

### 一、Mafile介绍

make命令执行时，需要一个Makefile文件，以告诉make命令需要怎么样的去编译和链接程序。

**规则：** >1、如果这个工程没有编译过，那么我们的所有c文件都要编译并被链接。  
> 2、如果这个工程的某几个c文件被修改，那么我们只编译被修改的c文件，并链接目标程序。  
> 3、如果这个工程的头文件，那么我们需要编译引用这个几个头文件的c文件，并链接目标程序。

#### 1.1、Makefile的规则

```
target ... : prerequisites ...  
    command
    ...
    ...
```

target也就是一个目标文件，可以是Object File，也可以是执行文件。还可以是一个标签(Label“伪目标”)。  
prerequisites就是，要生成那个target所需要的文件或是目标。  
command也就是make需要执行的命令。(任意的shell命令)  
这是一个文件的依赖关系，也就是说，target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command。prerequisites中如果有一个以上的文件比target文件要新的话，command命令就会被执行，这就是Makefile的规则，也就是Makefile中最核心的内容。

#### 1.2、示例

```
edit: main.o kdb.o command.o display.o \
    insert.o search.o files.o utils.o
    cc -o edit main.o kdb.o command.o display.o \
    insert.o search.o files.o utils.o

main.o : main.c defs.h
    cc -c main.c

kdb.o kdb.c defs.h command.h  
    cc -c kdb.c  

command.o : command.c defs.h command.h  
    cc -c command.c

display.o : display.c defs.h buffer.h  
    cc -c display.c

insert.o : insert.c defs.h buffer.h  
    cc -c insert.c   

search.o : search.c defs.h buffer.h   
    cc -c search.c  

files.o : files.c defs.h buffer.h command.h  
    cc -c files.c  

utils.o : utils.c defs.h  
    cc -c utils.c  

clean :
    rm edit main.o kdb.o command.o display.o \
    insert.o search.o files.o utils.o
```

**反斜杆(\\\)** 是换行符的意思。  
在这个Makefile中，目标文件(target)包含：执行文件edit和中间目标文件(\*o)， 依赖文件(prerequisites)就是冒号后面的.c文件和.h文件。每一个.o文件都有一组依赖文件，而这些.o文件又是执行文件edit的依赖文件。依赖关系实质上就是说明了目标文件由哪些文件生成的，换而言之，目标文件是哪些文件更新的。 在定义好依赖关系后，后续的那一行的定义如何生成目标文件的 **操作系统命令**，一定要一个 **Tab键作为开头**。记住，make并不管命令是怎么工作的，它只管执行所定义的命令。make会比较targets文件和prerequisites文件的修改日期，如果prerequisites文件的日期要比targets文件的日期要新，或者target不存在的话，那么，make就会执行后续的定义命令。

> clean不是一个文件，它只不过是一个动作的名字，有点像C语言的lable一样，其冒号hou什么也没有，那么make就不会自动去找文件的依赖性，也就不会自动执行其后所定义的命令。要执行其后的命令，就要在make命令后明显得指出这个lable的名字。常见的程序的打包与备份等命令。

#### 1.3、make是如何工作的？

在默认的方式下，也就是我们只输入make命令。那么，make会再当前目录下找名字叫“Makefile”或“Makefile”的文件。如果找到，它会找文件中的第一个目标文件(target)，如果目标文件不存在，或是所依赖的后面的.o文件的文件修改时间要比目标文件新，那么，make就会执行后面所定义的命令来生成目标文件。如果目标文件所依赖的.o文件也不存在，那么make会在当前的文件中找目标为.o文件的依赖性，如果找打则再根据那一个规则生成.o文件。

#### 1.4、Makefile中的变量

Makefile的变量也就是一个字符串，理解成C语言中的宏可能会更好。比如，我们声明一个变量，objects, OBJECTS, objs, OBJS, obj, 或者OBJ，我们在Makefile一开始就这样定义：

```
objects = main.o kbd.o command.o display.o \  
    insert.o search.o files.o utils.o
```

于是，我们就可以很方便地在我们的Makefile中以**“$(objects)”** 的方式来使用这个变量了,改良版的Makefile如下：

```
objects = main.o kbd.o command.o display.o \
    insert.osearch.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

main.o : main.c defs.h
    cc -c main.c

kdb.o kdb.c defs.h command.h  
    cc -c kdb.c  

command.o : command.c defs.h command.h  
    cc -c command.c

display.o : display.c defs.h buffer.h  
    cc -c display.c

insert.o : insert.c defs.h buffer.h  
    cc -c insert.c   

search.o : search.c defs.h buffer.h   
    cc -c search.c  

files.o : files.c defs.h buffer.h command.h  
    cc -c files.c  

utils.o : utils.c defs.h  
    cc -c utils.c

clean :
    rm edit $(objects)
```

#### 1.5、自动推导

GNU的make很强大，它可以自动推导文件以及文件依赖关系后面的命令，于是我们就没必要去在每一个.o文件后都写上类似的命令，因为make会自动识别，并自己推导命令。  
只要make看到一个.o文件，它就会自动把.c文件加在依赖关系中，如果make找到一个whatever.o，那么whatever.c，就会是whatever.o的依赖文件。并且cc -c whatever.c也会被推导出来，于是，简化版的Makefile如下：

```
objects = main.o kbd.o command.o display.o \
    insert.osearch.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

 main.o : defs.h
 kbd.o : defs.h command.h
 command.o : defs.h command.h
 display.o : defs.h buffer.h
 insert.o : defs.h buffer.h
 search.o : defs.h buffer.h
 files.o : defs.h buffer.h command.h
 utils.o : defs.h

.PHONY : clean
clean :
    rm edit $(objects)
```

`这种方法，也就是make的‘隐晦规则’。上面文件内容中，“.PHONY”表示，clean是个伪目标文件。`

#### 1.6、另类风格的Makefile

make可以自动推导命令，还可以将.h收拢起来。

```
objects = main.o kbd.o command.o display.o \
    insert.osearch.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

$(objects) : defs.h
kbd.o command.o files.o : command.h
display.o insert.o search.o files.o : buffer.h

.PHONY : clean  
    clean : rm edit $(objects)
```

这种风格，让Makefile变得很简单，但文件的依赖关系显的有点凌乱。

#### 1.7、清空目标文件的的规则

每个Makefile中都应该写一个清空目标的文件(.o和执行文件的规则)，这不仅便于重编译，也很利于保持文件的清洁。  
一般的风格：

```
clean:   
   rm edit $(objects)
```

更为稳健的做法是：

```
.PHONY : clean
clean :
    -rm edit $(objects)
```

`.PHONY意思是clean是一个“伪目标”。而在rm命令前加一个小减号的意思就是，也许某些文件出现了问题，但不要管，继续做后面的事。`当然，clean的规则不要放在文件的开头，不然，这就会变成make的默认目标。不成文的规矩是---“clean从来都是放在文件的最后”。

### 二、Makefile总述

#### 2.1、Makefile里有什么？

Makefile里主要包含五个东西：**显示规则、隐晦规则、变量定义、文件指示、注释**  
显示规则：显示规则说明了，如何生成一个或多的目标文件。这是由Makefile的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。  
隐晦规则：由于make有自动推导的功能，所以隐晦的规则可以让我们比较粗糙的简略地书写Makefile，这是由make所支持的。  
变量的定义：在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点c语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。  
文件指示：其包括三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；另一个是指根据某些情况制定Makefile中的有效部分，就像C语言中的预编译#if一样；还有就是定义一个多行的命令。  
注释：Makefile中只有行注释，和UNIX的shell脚本一样，其注释是用“#”字符，如果要在你的Makefile文件中使用“#”字符，可以用“\\#”。

#### 2.2、Makefile文件名

默认情况下，make的命令会在当前目录寻找文件名为“GNUmakefile”、“makefile”、“Makefile”的文件，在这个三个文件名中，最好使用“Makefile”这个文件名。  
>当然可以使用别名来书写Makefile，比如：“Make.linux”...等，如果要制定特定的Makefile，可以使用make的**“-f”和“--file”** 参数。如make -f Make.linux

#### 2.3、引用其他的Makefile

在Makefile使用include关键字可以把别的Makefile包含进来，这很像C语言的#include包含进来，include的语法是：  
`include <filename> filename可以是当前操作系统Shell的文件模式（可以保含路径和通配符）`  
在include前面可以有一些空字符，但是绝不能是[Tab]键开始。include和可以用一个或多个空格隔开。举个例子，你有这样几个Makefile：a.mk、b.mk、c.mk，还有一个文件叫foo.make，以及一个变量$(bar)，其包含了e.mk和f.mk，那么，下面的语句：  
`include foo.make *.mk $(bar)`  
等价于  
`include foo.make a.mk b.mk c.mk e.mk f.mk`  
make命令开始时，会把找寻include所指出的其它Makefile，并把其内容安置在当前的位置。就好像C/C++的#include指令一样。如果文件都没有指定绝对路径或是相对路径的话，make会在当前目录下首先寻找，如果当前目录下没有找到，那么，make还会在下面的几个目录下找：

1.如果make执行时，有“-I”或“--include-dir”参数，那么make就会在这个参数所指定的目录下去寻找。  
2.如果目录/include（一般是：/usr/local/bin或/usr/include）存在的话，make也会去找。  
如果有文件没有找到的话，make会生成一条警告信息，但不会马上出现致命错误。它会继续载入其它的文件，一旦完成makefile的读取，make会再重试这些没有找到，或是不能读取的文件，如果还是不行，make才会出现一条致命信息。如果你想让make不理那些无法读取的文件，而继续执行，你可以在include前加一个减号“-”。  
如：`-include <filename>`其表示，无论include过程中出现什么错误，都不要报错继续执行。和其它版本make兼容的相关命令是sinclude，其作用和这一个是一样的。

#### 2.4、环境变量

如果你的当前环境中定义了环境变量MAKEFILES，那么，make会把这个变量中的值做一个类似于include的动作。这个变量中的值是其它的Makefile，用空格分隔。只是，它和include不同的是，从这个环境变中引入的Makefile的“目标”不会起作用，如果环境变量中定义的文件发现错误，make也会不理。  
但是在这里我还是建议不要使用这个环境变量，因为只要这个变量一被定义，那么当你使用make时，所有的Makefile都会受到它的影响，这绝不是你想看到的。在这里提这个事，只是为了告诉大家，也许有时候你的Makefile出现了怪事，那么你可以看看当前环境中有没有定义这个变量。

#### 2.5、make的工作方式

> 1、读入所有的Makefile  
> 2、读入被include的其他Makefile  
> 3、初始化文件中的变量  
> 4、推导隐晦规则，并分析所有规则  
> 5、为所有的目标文件创建依赖关系链  
> 6、根据依赖关系，决定哪些目标要重新生成  
> 7、执行生成命令

1-5为第一阶段，6-7为第二阶段。

### 三、Makefile书写规则

规则包含两个部分，一个是依赖关系，一个是生成目标的方法。  
在Makefile中，规则的顺序是很重要的，因为，Makefile中只应该有一个最终目标，其他的目标都是被这个目标所连带出来的，所以一定要让make知道你的最终目标是什么。一般来说，定义在Makefile中的目标可能会很多，但是第一条规则中的目标将被确定为最终的目标。如果第一条规则中的目标有很多个，那么第一个目标会成为最终的目标。make所完成的也就是这个目标。

#### 3.1、规则的语法

```
target : prerequisites; command
    command
```

command是命令行，如果与“target： prerequisites”在一行，那么可以用分号做为分隔。

#### 3.2、在规则中使用通配符

如果我们定义一系列类似的文件，可以使用通配符，make支持三个通配符：“\*”, "?", "[...]".  
**"~"** 波浪号字符在文件名中也有比较特殊的用途，表示当前用户的home目录。

#### 3.3、文件搜寻

Makefile文件中的特殊变量“VPATH”就是完成这个功能的，如果没有指明这个变量，make只会在当前的目录中去寻找依赖文件和目标文件。如果定义了这个变量，那么make就会在当前目录下找不到的情况下，到所指定的目录中寻找文件。

`VPATH = src:../headers`定义指定了两个目录，“src”和“../headers”，目录由“冒号”分隔，当前目录永远是最高优先搜索的地方。  
另一种设置文件搜索路径的方式是使用make的“vpath”关键字。  
`1. vpath <pattern> <directions> 为符合模式<pattern>的文件指定搜索目录<directions>。`  
`2. vpath <pattern> 清楚符合模式<pattern>的文件的搜索目录。`  
`3. vpath 清楚所有已经被设置好了的文件搜索目录。`  
vapth使用方法中的< pattern>需要包含“%”字符。“%”的意思是匹配零或若干字符，例如，“%.h”表示所有以“.h”结尾的文件。< pattern>指定了要搜索的文件集，而< directories>则指定了的文件集的搜索的目录。例如：  
`vpath %.h ../headers`  
该语句表示，要求make在“../headers”目录下搜索所有以“.h”结尾的文件。

```
vpath %.c foo:bar
vpath %   blish
```

上面的语句则表示“.c”结尾的文件，先在“foo”目录，然后是“bar”目录，最后才是“blish”目录。

#### 3.4、伪目标

```
clean:
    rm *.o temp
```

伪目标的取名不能和文件名重名，不然其就失去了“伪目标”的意义。当然，为了避免和文件重名的这种情况，我们可以使用一个特殊的标记.PHONY来显示的指明一个目标是伪目标。

```
.PHONY : clean   
clean :
    rm *.o temp
```

只要有这个声明，不管是否有“clean”文件，要运行“clean”这个目标 ，只有“make clean”这样。  
伪目标一般没有依赖的文件，但是也可以将目标指定依赖文件。伪目标同样可以作为“默认目标”，只要将其放在第一个。一个Makefile生成若干个可执行文件的示例。

```
all : prog1 prog2 prog3
.PHONY : all  

prog1 : prog1.o utils.o
    cc -o prog1 prog1.o utils.o

prog2 : prog2.o  
    cc -o prog2 prog2.o

prog3 : prog3.o sort.o utils.o
    cc -o prog3 prog3.o sort.o utils.o
```

目标可以成为依赖，伪目标也可以成为依赖。

```
.PHONY : cleanall cleanobj cleandiff

cleanall : cleanobj cleandiff
    rm program

cleanobj :
    rm *.o  

cleandiff :
    rm *.diff
```

#### 3.5、多目标

Makefile的规则中的目标可不止一个，支持多目标，有可能多个目标同时依赖于一个文件，并且其生成的命令大体类似。于是就能把其合并起来。当然，多个目标的生成规则的执行命令是同一个，这可能会比较麻烦，但是可以使用自动化变量来“$@”实现，这个变量表示着目前规则中所有的目标的集合。

```
bigoutput littleoutput : text.g  
    generate text.g -$(subst output,,$@) > $@   
等价于  
bigoutput : text.g
    generate text.g -big > bigoutput

littleoutput : text.g
    generate text.g -little > littleoutput
```

其中，-$(subst output,,$@)中的“$”表示执行一个Makefile的函数，函数名为subst，后面的为参数。关于函数，将在后面讲述。这里的这个函数是截取字符串的意思，“$@”表示目标的集合，就像一个数组，“$@”依次取出目标，并执于命令。其中，-$(subst output,,$@)中的“$”表示执行一个Makefile的函数，函数名为subst，后面的为参数。关于函数，将在后面讲述。这里的这个函数是截取字符串的意思，“$@”表示目标的集合，就像一个数组，“$@”依次取出目标，并执于命令。

#### 3.6、静态模式

静态模式可以更加容易地定义多目标的规则，可以让规则变得更加的有弹性和灵活。  
语法：

```
<targets...> : <target-pattern> : <prereq-patterns...>   
    <commands>
...
```

targets定义了一系列的目标文件，可以有通配符。是目标的一个集合。  
target-parrtern是指明了targets的模式，也就是的目标集模式。  
prereq-parrterns是目标的依赖模式，它对target-parrtern形成的模式再进行一次依赖目标的定义。  
这样描述这三个东西，可能还是没有说清楚，还是举个例子来说明一下吧。如果我们的<target-parrtern>定义成“%.o”，意思是我们的集合中都是以“.o”结尾的，而如果我们的<prereq-parrterns>定义成“%.c”，意思是对<target-parrtern>所形成的目标集进行二次定义，其计算方法是，取<target-parrtern>模式中的“%”（也就是去掉了[.o]这个结尾），并为其加上[.c]这个结尾，形成的新集合。  
所以，我们的“目标模式”或是“依赖模式”中都应该有“%”这个字符，如果你的文件名中有“%”那么你可以使用反斜杠“\”进行转义，来标明真实的“%”字符。

```
objects = foo.o bar.o

all: $(objects)

$(objects): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
```

上面的例子中，指明了我们的目标从$object中获取，“%.o”表明要所有以“.o”结尾的目标，也就是“foo.o bar.o”，也就是变量$object集合的模式，而依赖模式“%.c”则取模式“%.o”的“%”，也就是“foobar”，并为其加下“.c”的后缀，于是，我们的依赖目标就是“foo.cbar.c”。而命令中的“$<”和“$@”则是自动化变量，“$<”表示所有的依赖目标集（也就是“foo.c bar.c”），“$@”表示目标集（也褪恰癴oo.o bar.o”）。于是，上面的规则展开后等价于下面的规则:

```
foo.o : foo.c
    $(CC) -c $(CFLAGS) foo.c -o foo.o

bar.o : bar.c
    $(CC) -c $(CFLAGS) bar.c -o bar.o
```

试想，如果我们的“%.o”有几百个，那种我们只要用这种很简单的“静态模式规则”就可以写完一堆规则，实在是太有效率了。“静态模式规则”的用法很灵活，如果用得好，那会一个很强大的功能。再看一个例子：

```
files = foo.elc bar.o lose.o

$(filter %.o,$(files)): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@

$(filter %.elc,$(files)): %.elc: %.el
    emacs -f batch-byte-compile $<
```

$(filter%.o,$(files))表示调用Makefile的filter函数，过滤“$filter”集，只要其中模式为“%.o”的内容。其的它内容，我就不用多说了吧。这个例字展示了Makefile中更大的弹性。

#### 3.8、自动生成依赖关系

https://yq.aliyun.com/articles/9243
