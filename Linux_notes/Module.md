驱动模块知识
------------

#### 一、内核加载模块的工作流程

1、内核加载模块，会运行驱动中的哪些函数，同理内核卸载模块？  
内核加载模块的工作流程，卸载模块的工作流程？

#### 二、驱动模块传参数

1、头文件 include/linux/moduleparam.h包含了向模块传递参数的函数，头文件include/linux/stat.h包含了函数参数perm的定义。  
2、函数原型：

```
module_param(name, type, perm)      \
module_param_named(name, name, type, perm)    //支持传递单个参数   
module_param_array(name, type, nump, perm)  \    
module_param_array_named(name, name, type, nump, perm)  \\支持传递多个参数
```

perm表示此参数在sysfs文件系统中所对应的文件节点的属性
