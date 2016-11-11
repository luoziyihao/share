# shell整理

### 常规

**ls,ps,cp,rm,cut,echo,cat,more,uniq,sort,find,cmp,wc,du**

- ls

```
ls -l

ls -a
```

- ps

```
ps -elf
```

- cp 

```
cp file1 file2

cp file1 dir/

```

- rm 

```
rm -rf

```

- cut 

```
cut -d " " -f number1,number2
```

- echo

```
echo "test"

echo ${test }

```

- cat

```
cat file

```

- more

```
j

k

ctrl f

ctrl b

ctrl d

ctrl u
```


- uniq

```
uniq

uniq -c

```

- sort

```
sort

```

- find 

```
find filepath

find filepath -type d[f] -cmin -3

```
 
- cmp

```
cmp -s 

```

- wc

```
wc -l 

```

- du

```
du -k -s

du -m -s
```

### 深入

**shell脚本语法,let,test,dirname,xargs,grep,head,tail,$,sleep,正则,awk,sed**

- shell脚本语法

```
shell是动态类型的，弱类型的语言，所有的变量都是字符串

有常规的语言结构，if,case,for,while

每一行代码基本上就是一条命令,其中分为shell自建命令如cd,echo等 ,和外部命令sort,awk等

```


- let

```
数字计算 let count+=1
```


- test 

```
test cmd 和[ cmd ]一起，一般用作if语句的条件判断

```

- dirname

```
dirname filename 获取文件的父目录,一般用来获取运行脚本的位置

```

- xargs

```
将标准输的每一条记录作为一个参数赋给单条命令处理

```

- grep

```
搜索命令 grep "match pattern" fileName

-i 忽略大小写

-v 过滤掉满足匹配条件的    

eg: 譬如说删掉目录下以.java结尾外的文件

ls -l|sed '/^d/d'|awk 'NR>1{print $NF}'|grep -v ".*\.java$"|xargs rm

ls -l|sed -e '/^d/d' -e '/\.java$/d'|awk 'NR>1{print $NF}'|xargs rm

```

- head

```
head -n linenumber 获取前linenumber条记录

```

- tail

```
tail -n linenumber 获取前linenumber条记录

```

- $

- sleep times

```
让当前进程阻塞相应时间，然后进入就绪队列
```

- 正则

```
在基于 shell,awk,sed的文本处理中，正则是地基。

```

- awk

```
一种可编程的列编辑器

```

- sed

```
一种可编程的行编辑器
```

### 案例

```
交付材料抽包工具

脚本转换工具

```

### 单行,小脚本，大脚本

```
去空行 sed -e '/^[\t \r]*$/d' <oldfile > newfile

统计目录下java文件的代码行数 find ./ -type f |sed -e '/\.java$/!d'|xargs cat |sed -e '/^[\t \r]*$/d'|wc -l

生成发交换核心配置文件,flw.xml配置文件

```

### 常见应用场景
```
可编程的重复性工作

```
