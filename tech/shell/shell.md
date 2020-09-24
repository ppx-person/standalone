
* IFS (Internal Field Separator)

Shell 的环境变量分为 set, env 两种，其中 set 变量可以通过 export 工具导入到 env 变量中。其中，set 是显示设置shell变量，仅在本 shell 中有效；
env 是显示设置用户环境变量 ，仅在当前会话中有效。

而 IFS 是一种 set 变量，当 shell 处理"命令替换"和"参数替换"时，shell 根据 IFS 的值，默认是 space, tab, newline 来拆解读入的变量，
然后对特殊字符进行处理，最后重新组合赋值给该变量。
 
