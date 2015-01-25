# find


搜索指定目录下的文件

--------------------------------


## 简单用法


#### 根据文件名搜索`-name`
```
find path -name "*.txt"
          -iname  忽略大小写
```

e.g.

```
$ find . -name "*.log"
./noinclude.log
```

#### 根据文件类型搜索`-type`
```
find . -type d
f 普通文件
l 符号链接
d 目录
c 字符设备
b 块设备
s 套接字
p Fifo
```

e.g.
```
$ find . -type l
./te
$ ll te
lrwxrwxrwx 1 ken ken 8 Jan 17 19:50 te -> test.txt
```


#### 根据文件大小搜索`-size`

```
find . -size +2k
+ 大于  -小于   无符号，恰好等于

b 块
c 字节
w 字（2字节）
k 千字节
M 兆字节
G 吉字节
```

e.g.
```
$ find . -size +92c
.
./noinclude.log
./language.txt
./text
./text/noinclude.txt
./include.py
```

#### 根据文件时间搜索`-atime/-mtime/-ctime`

```
find . -atime -7
- N天内  +N天前 无符号恰好 N天

计量单位 天
-atime 最近一次访问时间
-mtime 最后一次被修改时间
-ctime 文件元数据，最近一次修改时间

find . -atime -7  #最近七天内被访问的
find . -atime  7  #恰好在七天前
              +7  #超过七天
```


```
find . -amin +7
计量单位 分钟
-amin 访问时间
-mmin 修改时间
-cmin 变化时间

find . -amin +7 #访问时间超过7分钟的
find . -newer file.txt  #用于比较时间戳的参考文件，比参考文件更新的文件

```

#### 根据文件权限或者所有者搜索`-perm/-user`

```
find . -perm 644
find . -user test
```

#### 文件路径中包含`-path`

```
$ find . -path '*te*'
./test.txt
./data.sorted2
./text
./text/noinclude.txt
./data.sorted
./pattern_file
```

--------------------------------

## 更多选项


#### 否定参数, 取反`!`

```
find . ! -name "*.txt"
```

#### 多个匹配表达式
```
$ find . \( -name "*.txt" -o -name "*.py" \)
./language.txt
./test.txt
./text/noinclude.txt
./data.txt
./include.py
```

#### 设定搜索深度`-maxdepth`

```
# 注意, 放在其他选项前面
$ find . -maxdepth 1 -name "*.txt"
./language.txt
./test.txt
./data.txt
$ find . -maxdepth 2 -name "*.txt"
./language.txt
./test.txt
./text/noinclude.txt
./data.txt
```


--------------------------------


## 查找后执行


#### 查找后删除`-delete`

```
find . -name "*.swp" -delete
#注意：-delete位置一定是最后

#or
$ find . -name '*.swp' -exec rm -f {} \;
```



#### 搜索成功后执行命令`-exec`

```
find . -exec cp {} /data/backup \;
find . -iname "abc.txt" -exec md5sum {} \;

{} 将被替换成对应文件名
exec无法结合多个命=>可以将多个命令放入脚本，调用之
```


