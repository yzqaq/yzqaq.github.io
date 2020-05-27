---
title: shell教程
date: 2020-05-26 13:26:02
tags: ['技术']
categories: 教程
---
# shell 基础

## **Shell 变量**

**定义**
Shell 变量分为**系统变量**和**自定义变量**。系统变量有$HOME、$PWD、$USER等，显示当前 Shell 中所有变量：`set` 。 
变量名可以由字母、数字、下划线组成，不能以数字开头。
**基本语法**

- **定义变量：**变量名=变量值，等号两侧不能有空格，变量名一般习惯用大写。
- **删除变量：**unset 变量名 。
- **声明静态变量：**readonly 变量名，静态变量不能unset。
- **使用变量：**$变量名

**将命令返回值赋给变量（重点）**

- A=\`ls\` 反引号,执行里面的命令
- A=$(ls) 等价于反引号
- A="ls" 存储一条命令
- A="ls;pwd" 
- echo "$(eval $A)" 使用eval执行多条命令

**Shell 环境变量**

**基本语法**

1. export 变量名=变量值，将 Shell 变量输出为环境变量。
2. source 配置文件路径，让修改后的配置信息立即生效。
3. echo $变量名，检查环境变量是否生效

**位置参数变量**
**基本语法**

- $n ：$1 代表命令本身、$1-$9 代表第1到9个参数，10以上参数用花括号，如 ${10}。
- $* ：命令行中所有参数，且把所有参数看成一个整体。
- $@ ：命令行中所有参数，且把每个参数区分对待。
- $# ：所有参数个数。

**实例：**
编写 Shell 脚本 positionPara.sh ，输出命令行输入的各个参数信息。

```bash
#!/bin/bash     
# 输出各个参数 
echo $0 $1 $2 
echo $* 
echo $@ 
echo 参数个数=$#
```


运行：

```text
chmod +x positionPara.sh 
./positionPara.sh 10 20
```


运行结果：

```text
./positionPara.sh 10 20 
10 20 
10 20 
参数个数=2
```


**预定义变量**
**定义**
在赋值定义之前，事先在 Shell 脚本中直接引用的变量。
**基本语法**

- $$ ：当前进程的 PID 进程号。
- $! ：后台运行的最后一个进程的 PID 进程号。
- $? ：最后一次执行的命令的返回状态，0为执行正确，非0执行失败。

**实例：**
编写 Shell 脚本 prePara.sh ，输出命令行输入的各个参数信息。

```bash
#!/bin/bash     
echo 当前的进程号=$$ 
# &：以后台的方式运行程序 
./hello.sh 
& echo 最后一个进程的进程号=$! 
echo 最后执行的命令结果=$?
```


运行结果：

```text
当前的进程号=41752 
最后一个进程的进程号=41753 
最后执行的命令结果=0 # hello world!
```


**运算符**
**基本语法**

- $((运算式)) 或 $[运算式]
- expr m + n 注意 expr 运算符间要有空格
- expr m - n
- expr \*，/，% 分别代表乘，除，取余

**实例**

```bash
# 第1种方式 $(()) 
echo $(((2+3)*4))   

# 第2种方式 $[]，推荐 
echo $[(2+3)*4]  

# 使用 expr 
TEMP=`expr 2 + 3` 
echo `expr $TEMP \* 4`
```



## **条件判断**

**基本语法**
[ condition ] 注意condition前后要有空格。非空返回0，0为 true，否则为 false 。
**实例**

```bash
#!/bin/bash 
if [ 'test01' = 'test' ] 
then
     echo '等于' 
fi  

# 20是否大于10 
if [ 20 -gt 10] 
then
     echo '大于' 
fi  

# 是否存在文件/root/shell/a.txt 
if [ -e /root/shell/a.txt ] 
then
     echo '存在' 
fi  

if [ 'test02' = 'test02' ] && echo 'hello' || echo 'world' 
then
     echo '条件满足，执行后面的语句' 
fi
```

运行结果：

```bash
大于 
hello 
条件满足，执行后面的语句
```

**逻辑运算符**

- -o或or，或者，同||
- -a或and，与，同&&
- ! ，非

**实例**

```shell
# 三种等价的写法
# 第一种 用-o
if [ 10 -gt 5 -o 10 -gt 4 ];
then 
echo ""
fi

# 第二种 用or
if [ 10 -gt 5 or 10 -gt 4 ];
then 
echo ""
fi

# 第三种种 用||
if [ 10 -gt 5 ] || [ 10 -gt 4 ];
then 
echo ""
fi
```

整数判断，下面只能用于整数

- -eq 两数是否相等
- -ne 两数是否不等
- -gt 前者是否大于后者（greater then）
- -lt 前面是否小于后者（less than）
- -ge 前者是否大于等于后者（greater then or equal）
- -le 前者是否小于等于后者（less than or equal）

字符串判断str1 exp str2：

- -z "$str1" str1是否为空字符串
- -n "$str1" str1是否不是空字符串
- "$str1" == "$str2" str1是否与str2相等
- "$str1" != "$str2" str1是否与str2不等
- "$str1" =~ "str2" str1是否包含str2

特别注意，exp的前后有空格， **字符串变量最好用引号引起来**，因为一旦字符串中有空格，这个表达式就错了。

```bash
str1="hello word"
str2="hello"
# 因为字符串变量没有用引号引起来
# 运行后报错
# 第 5 行: [: 参数太多
if [ $str1  ==  $str2 ];
then
echo "1"
else
echo "2"
fi
```

文件目录判断：filename

- -f $filename 是否为文件
- -e $filename 是否存在
- -d $filename 是否为目录
- -s $filename 文件存在且不为空
- ! -s $filename 文件是否为空

## 流程控制

### **if 判断**

**基本语法**

```bash
if [ 条件判断式 ];then   
    程序   
fi

# 或者（推荐）
if [ 条件判断式 ]
then
    程序
elif [ 条件判断式 ]
then
    程序
fi
```

**实例**

编写 Shell 程序：如果输入的参数大于60，输出“及格”，否则输出“不及格”。

```bash
#!/bin/bash
if [ $1 -ge 60 ]
then
    echo 及格
elif [ $1 -lt 60 ]
then
    echo "不及格" 
fi
```

### **case 分支**

**基本语法**

```bash
case $变量名 in
"值1")
如果变量值等于值1，则执行此处程序1
;;
"值2")
如果变量值等于值2，则执行此处程序2
;;
...省略其它分支...
*)
如果变量值不等于以上列出的值，则执行此处程序
;;
esac
```

**实例**

当命令行参数为1时输出“周一”，2时输出“周二”，其他情况输出“其它”。

```bash
case $1 in
"1")
echo 周一
;;
"2")
echo 周二
;;
*)
echo 其它
;;
esac
```

### **for 循环**

**基本语法**

```bash
# 语法1
for 变量名 in 值1 值2 值3...
do
    程序
done

# 语法2
for ((初始值;循环控制条件;变量变化))
do
    程序
done
# 语法3
# 等价于 
# for ((i=1;i<=5;i++))
for i in {1..5}
do
程序
done
# 语法4
# 等价于
# for ((i=1;i<=5;i+=2))
for i in {1..5..2}
do 
程序
done
```

**实例**

1. 打印命令行输入的参数。

```bash
#!/bin/bash  

# 使用$* 
for i in "$*" 
do     
    echo "the arg is $i" 
done 
echo "=================="  

# 使用$@ 
for j in "$@" 
do     
    echo "the arg is $j" 
done
```

运行结果（回顾一下 $* 和 $@ 的区别）： 

```text
the arg is 1 2 3 
================== 
the arg is 1 
the arg is 2 
the arg is 3
```

2. 输出从1加到100的值。

```bash
#!/bin/bash 
SUM=0  
for ((i=1;i<=100;i++)) 
do     
    SUM=$[$SUM+$i] 
done 

echo $SUM
```

3. 输出1到5

```bash
for i in {1..5}
do
echo $i
done
# 输出为
1
2
3
4
5
```



### **while 循环**

**基本语法**

```bash
# 当条件表达式为false退出循环
while [ 条件判断式 ]
do
    程序
done 

# 类似的
# 当条件表达式为true退出循环
until [ 条件表达式 ]
do
程序
done

```

**实例**

输出从1加到100的值。

```bash
#!/bin/bash
SUM=0
i=0

while [ $i -le $1 ]
do
    SUM=$[$SUM+$i]
    i=$[$i+1]
done       
echo $SUM
```

## **读取控制台输入**

**基本语法**

read(选项)(参数) 
**选项**

- -p：指定读取值时的提示符
- -t：指定读取值时等待的时间（秒），如果没有在指定时间内输入，就不再等待了。

**参数**

- 变量名：读取值的变量名

**实例**

读取控制台输入一个num值。

```text
#!/bin/bash

read -p "请输入一个数num1=" NUM1
echo "你输入num1的值是：$NUM1"

read -t 10 -p "请在10秒内输入一个数num2=" NUM2
echo "你输入num2的值是：$NUM2"
```

运行结果：

```text
请输入一个数num1=10
你输入num1的值是：10
请在10秒内输入一个数num2=20
你输入num2的值是：20
```

## **函数**

和其它编程语言一样，Shell 编程有系统函数和自定义函数，本文只举两个常用系统函数。

### **系统函数**

- basename，删掉路径最后一个 / 前的所有部分（包括/），常用于获取文件名。
  **基本语法**

- - basename [pathname] [suffx]
  - basename [string] [suffx]
  - 如果指定 suffx，也会删掉pathname或string的后缀部分。

**实例**

```text
# basename /usr/bin/sort  
sort  

# basename include/stdio.h  
stdio.h  

# basename include/stdio.h .h 
stdio
```

- dirname，删掉路径最后一个 / 后的所有部分（包括/），常用于获取文件路径。
  **基本语法**

- - dirname pathname
  - 如果路径中不含 / ，则返回 '.' （当前路径）。

**实例**

```text
# dirname /usr/bin/  
/usr  

# dirname dir1/str dir2/str 
dir1 
dir2  

# dirname stdio.h 
.
```

### **自定义函数**

**基本语法**

```text
[ function ] funname[()]
{
    Action;
    [return int;] 
}

# 调用
funname 参数1 参数2...
# 函数返回值只支持0-255，
# 通过 val=$(funname) 获取返回值
```

**实例**

计算输入两个参数的和。

```text
#!/bin/bash

function getSum(){
    SUM=$[$n1+$n2]
    echo "sum=$SUM"
}   

read -p "请输入第一个参数n1：" n1
read -p "请输入第二个参数n2：" n2

# 调用 getSum 函数
getSum $n1 $n2
```

## 注释

shell通过#来注释一行内容，前面我们已经看到过了：

```bash
 #!/bin/bash`
# 这是一行注释
:'
这是
多行
注释
'
ls
:<<EOF
这也可以
达到
多行注释
的目的
EOF
```

## 其他

### 日志保存

脚本执行后免不了要记录日志，最常用的方法就是重定向。以下面的脚本为例：

```bash
# !/bin/bash
#test.sh
lll #这个命令是没有的，因此会报错`
date
```







方式一，将标准输出保存到文件中，打印标准错误：

1. `./test.sh > log.dat`



这种情况下，如果命令执行出错，错误将会打印到控制台。所以如果你在程序中调用，这样将不会讲错误信息保存在日志中。

方式二，标准输出和标准错误都保存到日志文件中：

1. `./test.sh > log.dat 2>&1`



2>&1的含义

```
在shell中有默认的文件描述符分别如下
0 - 标准输入
1 - 标准输出
2 - 标准错误输出
2 > &1的意思是将标准错误输出的内容重定向到标准输出里
```



方式三，保存日志文件的同时，也输出到控制台：

1. `./test.sh |tee log.dat`



### 脚本执行

最常见的执行方式前面已经看到了：

1. `./test.sh`



其它执行方式：

1. `sh test.sh #在子进程中执行`
2. `sh -x test.sh #会在终端打印执行到命令，适合调试`
3. `source test.sh #test.sh在父进程中执行`
4. `. test.sh #不需要赋予执行权限，临时执行`



### 脚本退出码

很多时候我们需要获取脚本的执行结果，即退出状态，通常0表示执行成功，而非0表示失败。为了获得退出码，我们需要使用exit。例如：

```bash
#!/bin/bash
function myfun()
{
if[ $# -lt 2 ]
then
echo "para num error"
exit 1
fi
echo "ok"
exit 2
}
if [ $# -lt 1 ]
then
echo "para num error"
exit 1
fi
returnVal=`myfun aa`
echo "end shell"
exit 0


```



这里需要特别注意的一点是，使用

```bash
returnVal=`myfun aa`
```

这样的句子执行函数，即便函数里面有exit，它也不会退出脚本执行，而只是会退出该函数，这是因为**exit是退出当前进程**，而这种方式执行函数，相当于fork了一个子进程，因此不会退出当前脚本。最终结果就会看到，无论你的函数参数是什么最后end shell都会打印。

```bash
./test.sh;echo $?
0
```

这里的0就是脚本的执行结果。

### **脚本失败时即退出**

可以在脚本的开头设置如下：

```text
set -e
```

举个例子：

```text
#！/bin/bash
set -e
lp    #这里运行会出错
date
```

这种情况下，运行一旦出错就会退出，不放过一个问题：

```text
$ ./test.sh
lp: Error - no default destination available.
```

当然了，这也有不好的地方，有时候命令执行本来就可能是失败的，你还是希望它继续，可以临时加上|| true：

```text
#！/bin/bash
set -e
lp  || true  
date
```

不过个人觉得这样的设置用处不是特别大，因为很多时候就是需要处理不同的错误情况，而这样只能要么遇到错误退出，要么认为正确，导致无法走到异常分支。
通过set +e设置回来：

```text
set -e
#command
set +e
#other command
```

### **打印脚本执行过程**

调试阶段，你可能想知道整个过程是执行了哪些命令，每条命令都具体执行了什么，可以利用下面的方式执行：

```text
sh -x test.sh
```

或者，和上面类似，在开头加上set -x：

```powershell
//来源：公众号【编程珠玑】
//作者：守望先生
#！/bin/bash
set -x
if [ $# -lt 1 ]
then
   echo  "no para"
else
   echo "para 1 $1"
fi
```

执行时，输出如下：

```text
+ [ 0 -le 1 ]
+ echo no para
no para
```

前面带+的内容就是命令实际执行的，你可以看到比较条件是什么，变量被展开成了具体内容，走到了哪个分支，非常清楚。

### **显示未定义的变量**

shell中变量没有定义，仍然是可以使用的，但是它的结果可能不是你所预期的。举个例子：

```powershell
#！/bin/bash
if [ "$var" = "abc" ]
then
   echo  " not abc"
else
   echo " abc "
fi
```

这里本来想判断var的内容是否为abc，实际上var并没有定义，但是在这里使用并没有报错，如果我们想早点发现这类问题，避免在复杂的脚本中问题被掩盖，那么可以在开头加上
：

```text
set -u
```

再次运行就会提示：

```text
test.sh: 3: test.sh: num: parameter not set
```

再想象一下，你本来想删除：

```text
rm -rf $dir/*
```

然后dir是空的时候，变成了什么？

是不是有种后背发凉的感觉？

>  来源：公号【编程珠玑】
>  

### **管道命令一个失败时整个失败**

有时候我们可能会执行类似这样的命令：

```text
cat test.sh |grep if | cut -d ';' -f 2
```

三条命令一行执行，如果我们希望在其中一条失败，整个命令就失败，而避免执行后面无意义的命令，那么可以在开始设置：

```text
set -o pipefail
```

不设置的情况下，cat test.sh即使执行失败了，后面的grep实际上还会继续执行，可能会导致一些意想不到的情况发生，如果不想这样的情况发生，那么这样设置是有帮助的。

### **对于静态变量使用readonly**

通常我们会在脚本开头定义一些静态变量：

```text
MY_PATH=/usr/bin
```

而为了避免MY_PATH被意外修改，可以这样：

```text
readonly MY_PATH=/usr/bin
```

这样的话，一旦后面有命令尝试修改，就会报错。

```text
#!/bin/bash
readonly MY_PATH=/usr/bin
MY_PATH=/usr/local/bin
```

运行一下试试：

```text
$ sh test.sh
test.sh: 3: test.sh: MY_PATH: is read only
```

看，给你提示了！

### **给变量设置可选的初始值**

例如：

```text
name=${1:-shouwang}
echo "${name}"
```

这里让name为$1，即第一个参数，而当它为空时，另name为shouwang。

### **多条命令执行使用&&**

例如：

```text
cmd0;cmd1;cmd1
```

这里如果cmd0失败了，后面的命令仍然会执行，而如果不希望后面的命令执行，可以使用：

```text
cmd0 && cmd1 && cmd1
```

# linux 命令



# 参考

[作者：守望 ](https://www.zhihu.com/question/387878326/answer/1162110940 )