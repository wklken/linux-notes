# awk

文本处理工具, 逐行处理(读取一行, 处理, 进入下一行, 直到文件结尾)



基本结构
```
awk -F '-' 'BEGIN{statements} {statements} END{statements}' file

0. -F 可选, 指定分隔符, 不指定使用默认分隔符
1. BEGIN/END可选
2. 表达式中单引号可以换成双引号
3. BEGIN -> 每一行，执行statements -> 执行END
```
获取帮助`man awk`

需要了解的概念
```
行(记录Record)

分隔符(FS)
默认空格,可指定 -F ':' 或 -F '[+$]'

列(域Field)
$0全部, $1第一列, $NF最后一列, 是可以增加和修改的


输出分隔符(OFS)
默认空格
```


awk特殊变量
```
NR  nuber of records, 记录数, 行号
NF  number of fields, 字段数
$0  当前行文本
$1  第一字段
$2  第二字段
$NF 最后一个字段

FILENAME 当前输入文件的名称
FNR      当前输入文件记录数
FS       字段分隔字符,等价于命令⾏行-F选项
RS       记录分隔符, 默认"\n"
OFS      输出字段分隔符，默认" "
ORS      输出记录分隔符，默认"\n"
```

内建函数

```
内建函数

length(string)                 #返回 string的长度
index(string,search_str)       #返回string中search_str的第一个位置
split(string,array,delimiter)  #用界定符生成一个字符串列表
substr(string, start, end)  #子串
sub(regex, replacement_str, str) #正则替换首个匹配位置
gsub(regex, replacement_str, string)    #在整个$0中全部替换
match(string, regex)    #检查是否能够匹配字符串
tolower(string)     #转小写
toupper(string)     #转大写
```


测试文件(\t分隔)
```
$ cat data.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
900:6:a1
1000:2:a5
```

## 简单用法

打印第二列
```
$ awk -F ':' '{print $2}' data.txt
3
1
4
5
6
2
```

打印所有列
```
$ awk -F ':' '{print $0}' data.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
900:6:a1
1000:2:a5
```

打印行号
```
$ awk '{print NR, $0}' data.txt
1 5000:3:a1
2 200:1:a3
3 1000:4:a3
4 200:5:a2
5 900:6:a1
6 1000:2:a5
```

输出前3行
```
$ awk 'NR<=3' data.txt
5000:3:a1
200:1:a3
1000:4:a3
```


将最后一列赋值(默认OFS为空格)
```
$ awk -F ':' '{$3="a"; print $0}' data.txt
5000 3 a
200 1 a
1000 4 a
200 5 a
900 6 a
1000 2 a

$ awk -F ':' -v 'OFS=:' '{$3="a"; print $0}' data.txt
5000:3:a
200:1:a
1000:4:a
200:5:a
900:6:a
1000:2:a
```

拼接字符串
```
$ awk -F ':' '{print $1"-"$2;}' data.txt
5000-3
200-1
1000-4
200-5
900-6
1000-2
```

增加一列
```
$ awk -F ':' -v 'OFS=:' '{$4="add"; print $0;}' data.txt
5000:3:a1:add
200:1:a3:add
1000:4:a3:add
200:5:a2:add
900:6:a1:add
1000:2:a5:add
```


## BEGIN/END
统计行数
```
$ awk 'END{print NR}' data.txt
6
```

求第二列的和/平均
```
$ awk -F ':' 'BEGIN{total=0;}{total+=$2;}END{print total;}' data.txt
21

$ awk -F ':' 'BEGIN{total=0;}{total+=$2;}END{print total/NR;}' data.txt
3.5
```

## 匹配/正则匹配

精确匹配
```
# 打印第3列为`a1`的记录
$ awk -F ':' '{if($3=="a1"){print $0}}' data.txt
5000:3:a1
900:6:a1
```

模糊匹配
```
# 所有包含`5`的记录
$ awk -F ':' '{if($0 ~ /5/){print $0}}' data.txt
5000:3:a1
200:5:a2
1000:2:a5

# `5`开头
$ awk -F ':' '{if($0 ~ /^5/){print $0}}' data.txt
5000:3:a1

# 5或1开头
$ awk -F ':' '{if($0 ~ /^[51]/){print $0}}' data.txt
5000:3:a1
1000:4:a3
1000:2:a5
```

不匹配, 所有不包含`5`的记录
```
$ awk -F ':' '{if($0 !~ /5/){print $0}}' data.txt
200:1:a3
1000:4:a3
900:6:a1
```

多条件
```
$ awk -F ':' '{if($1 == 1000 && $3 == "a3"){print $0}}' data.txt
1000:4:a3
```

## 使用函数

首列字符串长度
```
$ awk -F ':' '{print length($1);}' data.txt
4
3
4
3
3
4
```

截取首列的前两个字符
```
$ awk -F ':' '{print substr($1, 0, 2);}' data.txt
50
20
10
20
90
10
```

替换
```
$ awk '{gsub(/a/, "b"); print $0}' data.txt
5000:3:b1
200:1:b3
1000:4:b3
200:5:b2
900:6:b1
1000:2:b5
```

转换大小写
```
$ awk -F ':' '{$3=toupper($3);print $0;}' data.txt
5000 3 A1
200 1 A3
1000 4 A3
200 5 A2
900 6 A1
1000 2 A5

$ awk -F ':' -v 'OFS=:'  '{$3=toupper($3);print $0;}' data.txt
5000:3:A1
200:1:A3
1000:4:A3
200:5:A2
900:6:A1
1000:2:A5
```

## 其他

涉及数组/复杂表达式等更复杂的情况, 建议上`python/ruby/perl`


## 资料

- [awk简明教程](http://coolshell.cn/articles/9070.html)

