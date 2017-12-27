Linux命令
---------

### 一、ls命令

1、ls -a与ls -A区别？； -a：列出指定目录下的所有文件和子目录，包含隐藏文件。 -A：列出指定目录下的所有文件和子目录，包含隐藏文件，但不包含当前目录（.）和上一级目录（..）。  
2、文件类型中的符号连接后面加“@”，管道文件后面标记“|”，Scoket文件后面标记“=”？  
3、-n：文件属主和属组使用相应的UID和GID表示？ 4、![image](http://p1jvdr9xx.bkt.clouddn.com/linux_ls.jpg)

5、查看platform设备命令：ls/sys/devices/platform；/dev目录下面存放的是设备节点。

### 二、文件查找命令

1、查找名为a.c的文件：find -name "a.c"  
2、在当前目录下搜含有abc字样的文件：grep "abc" * -nR；-n表示哪一行，R递归到当前目录下及子目录下所有的文件
