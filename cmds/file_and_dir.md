# 文件和目录基本操作

## file

#### 1. 文件操作

##### touch

创建文件, 将文件访问和修改时间更新为当前时间

```
# 首次, 创建, 空文件
$ touch test.txt
$ ll test.txt
-rw-rw-r-- 1 ken ken 0 Jan 17 19:44 test.txt

# 第二次, 更新时间(不会对内容造成影响)
$ touch test.txt
$ ll test.txt
-rw-rw-r-- 1 ken ken 0 Jan 17 19:45 test.txt

# 修改成指定时间(YYMMDDhhmm)
$ touch -t 1412311230 test.txt
$ ll test.txt
-rw-rw-r-- 1 ken ken 0 Dec 31 12:30 test.txt
```


##### ln

创建链接
限制(不允许给目录创建硬链接, 只有在同一文件系统中的文件之间才能创建链接)
```
$ cat a1
1
$ ln a1 a2
```

建立软链接（soft link）/ 符号链接（symbolic link), 格式`ln -s target symbolic_link_name`
```
$ ln -s test.txt te
$ ll te
lrwxrwxrwx 1 ken ken 8 Jan 17 19:48 te -> test.txt

# 再次执行, 已存在, 报错
$ ln -s test.txt te
ln: creating symbolic link `te': File exists
# 强制
$ ln -sf test.txt te
$ ll te
lrwxrwxrwx 1 ken ken 8 Jan 17 19:50 te -> test.txt
```



##### rm

remove, 可以用来删除文件和目录

```
rm file1

# 递归删除空目录
rm -r dir1

# 强删, 慎用
rm -rf fileordir
```
##### mv

move, 将一个地方的文件移动到另外一个地方去

```
# 一个文件
$ mv data.txt text/

# 多个文件
$ mv data.txt data.sorted text/

# 移动并重命名
$ mv data.txt text/data
```

#### cp

copy, 从一个地方复制一个文件到另外一个地方

```
$ cp data.txt data1.txt

$ cp data.txt /tmp/
```

拷贝整个目录
```
$ cp -r dir/ /tmp/
```

快速备份一个文件
```
cp some_file_name{,.bk}
```


#### 2. 文件查看


##### cat

concatenate(拼接)，连接两个或者更多文本文件或者以标准输出形式打印文件的内容

语法: `cat file1 file2 file3`

```
$ echo "test" | cat
test

$ cat data.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
900:6:a1
1000:2:a5

# 加行号 -n
$ cat -n data.txt
     1  5000:3:a1
     2  200:1:a3
     3  1000:4:a3
     4  200:5:a2
     5  900:6:a1
     6  1000:2:a5
```

重定向到另一个文件

```
$ cat data.txt > bk.txt
```

##### head

查看文件前N行

```
# 前10行
$ head data.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
900:6:a1
1000:2:a5

# 前2行
$ head -2 data.txt
5000:3:a1
200:1:a3

# 扣除最后2行
$ head -n -2 data.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
```

##### tail

查看文件尾

```
# 最后10行
$ tail data.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
900:6:a1
1000:2:a5

$ tail -2 data.txt
900:6:a1
1000:2:a5
```

实时动态查看变更
```
tail -f growing_file
```


##### wc

Word Count, 统计

```
# line, 行数
$ wc -l data.txt
6 data.txt

# word, 单词数
$ wc -w data.txt
6 data.txt

# char, 字符数
$ wc -c data.txt
57 data.txt

# overview
$ wc data.txt
 6  6 57 data.txt
```

##### file

打印文件类型信息

```
$ ll a /usr/bin/python te
-rw-rw-r-- 1 ken  ken     4 Jan 17 19:37 a
lrwxrwxrwx 1 ken  ken     8 Jan 17 19:50 te -> test.txt
-rwxr-xr-x 2 root root 4864 Jan 22  2014 /usr/bin/python

$ file a
a: ASCII text

$ file /usr/bin/python
/usr/bin/python: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.18, stripped

$ file te
te: symbolic link to `test.txt'

# 不打印文件名
$ file -b te
symbolic link to `test.txt'
```


#### 3.其他

basename, 读取路径中最后一部分

```
$ ll data.txt
-rw-rw-r-- 1 ken ken 57 Jan 17 17:47 data.txt

$ basename data.txt
data.txt

$ basename /usr/bin/python
python
$ basename /usr/bin/
bin
```

readlink, 读取链接对应真实路径

```
$ ll te
lrwxrwxrwx 1 ken ken 8 Jan 17 19:50 te -> test.txt

$ readlink te
test.txt
```

diff, 文件对比, 生成文件差异
```
# 非一体化
$ cat a
1
2
$ cat b
0
2
$ diff a b
1c1
< 1
---
> 0

# 一体化
$ diff -u a b
--- a   2015-01-17 19:37:49.447931586 +0800
+++ b   2015-01-17 19:37:56.638926172 +0800
@@ -1,2 +1,2 @@
-1
+0
 2

# 使用patch将命令应用于任意一个文件
diff -u version1.txt version2.txt > version.patch
patch -p1 version1.txt < version.patch
```

comm, 两文件之间比较, 输出3列`onleA \t onlyB \t bothAB`
```
$ cat a
1
2
$ cat b
0
2
$ comm a b
    0
1
        2

# a & b
$ comm a b -1 -2
2

# a - b
$ comm a b -2 -3
1

# b - a
$ comm a b -1 -3
0

```

cmp, 文件比较相等判断
```
$ cmp a a
$ echo $?
0
$ cmp a b
a b differ: byte 1, line 1
$ echo $?
1
```

----------------------

## dir

#### ls 查看当前目录下内容

List Directory Contents,

文件列表
```
$ ls
a  b  data.sorted  data.sorted2  data.txt  include.py  language.txt  noinclude.log  pattern_file  text
```

文件明细列表`-l` 详情模式(long listing fashion)列出文件夹的内容
```
$ ls -l
total 40
-rw-rw-r-- 1 ken ken   11 Jan 17 17:52 a
-rw-rw-r-- 1 ken ken    6 Jan 17 17:22 b
-rw-rw-r-- 1 ken ken   92 Jan 17 17:12 data.sorted
-rw-rw-r-- 1 ken ken   92 Jan 17 17:13 data.sorted2
....
```

包含隐藏文件`-a` 列出文件夹里的所有内容
```
$ ls -al
total 48
drwxrwxr-x 3 ken ken 4096 Jan 17 19:18 .
drwxrwxr-x 4 ken ken 4096 Jan 17 11:51 ..
-rw-rw-r-- 1 ken ken   11 Jan 17 17:52 a
-rw-rw-r-- 1 ken ken    6 Jan 17 17:22 b
-rw-rw-r-- 1 ken ken    0 Jan 17 19:18 .hidden
....
```

只查看目录
```
$ ls -d */
text/
```

按更新时间倒序`-t`
```
$ ll -t
total 40
-rw-rw-r-- 1 ken ken   11 Jan 17 17:52 a
-rw-rw-r-- 1 ken ken   57 Jan 17 17:47 data.txt
```


附: ls文件的内容关系
```
- 普通文件
d 目录
c 字符设备
b 块设备
l 符号链接
s 套接字
p 管道

文件权限序列
rwx
rwS  setuid(S)，特殊权限, 出现在x的位置, 允许用户以其拥有者的权限来执行文件, 即使这个可执行文件是由其他用户运行的

目录
r,允许读取目录中文件和子目录列表
w,允许在目录中创建或删除文件或目录
x,指明是否可以访问目录中的文件和子目录
rwt/rwT 粘滞位，只有创建该目录的用户才能删除目录中的文件，即使用户组和其他用户也有写权限，典型例子/tmp, 写保护
```


#### mkdir 创建目录

make directory, 在命名路径下创建新的目录

注意：目录只能在用户拥有写权限的目录下才能创建


```
$ mkdir dirpath

# -p 一次创建多级, 如果上级目录不存在自动创建
$ mkdir -p dirpath1/dirpath2

# 一次多个目录
$ mkdir -p /tmp/{test,test1,test2}
```

#### rmdir 删除目录

remove directory, 只能删除空目录，如果目录不为空（存在文件或者子目录），那么该命令将拒绝删除指定的目录

```
$ rmdir dirpath
```

#### pwd 当前工作目录

print working directory, 显示当前工作目录的全路径
```
$ pwd
/Users/ken/workspace/linux-notes/cmds
```

#### cd 目录跳转

```
# 某个路径
$ cd /home/ken

# 上一级目录
$ cd ..

# home目录
$ cd
$ cd ~

# 上一个目录(进入当前目录前)
$ cd -


```

#### tree 打印目录树
可能需要自行安装
```
$ tree
.
├── a
├── b
├── data.txt
├── include.py
├── language.txt
├── noinclude.log
├── pattern_file
├── te -> test.txt
├── test.txt
└── text
    └── noinclude.log

1 directory, 13 files

# tree -h 同时打印文件/目录大小

# 打印某类文件
$ tree -P "*.txt"
.
├── data.txt
├── language.txt
├── test.txt
└── text
    └── noinclude.txt

1 directory, 4 files
```


