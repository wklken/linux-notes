# sort

用于排序

语法:
```
sort [options] [file(s)]
```

获取帮助`man sort`

测试文件(\t分隔)

```
131 Python
54  JavaScript
37  Go
18  C
11  Shell
10  C++
5   Java
3   Scala
2   CoffeeScript
1   Haskell
```


## 简单用法

基本(默认字典序)
```
$ sort data.txt
10  C++
11  Shell
131 Python
18  C
1   Haskell
2   CoffeeScript
37  Go
3   Scala
54  JavaScript
5   Java
```

逆序
```
$ sort -r data.txt
5   Java
54  JavaScript
3   Scala
37  Go
2   CoffeeScript
1   Haskell
18  C
131 Python
11  Shell
10  C++
```

按数字大小排序`-n`
```
$ sort -n data.txt
1   Haskell
2   CoffeeScript
3   Scala
5   Java
10  C++
11  Shell
18  C
37  Go
54  JavaScript
131 Python
```

去重(源文件一行重复) `-u`
```
$ sort data.txt
10  C++
11  Shell
11  Shell
131 Python
18  C
1   Haskell
2   CoffeeScript
37  Go
3   Scala
54  JavaScript
5   Java

$ sort -u data.txt
10  C++
11  Shell
131 Python
18  C
1   Haskell
2   CoffeeScript
37  Go
3   Scala
54  JavaScript
5   Java

# or
$ sort data.txt | uniq
```

忽略前导空白
```
sort -b data.txt
```


排序并输出
```
$ sort data.txt > data.sorted

$ sort -o data.sorted2 data.txt
```



## 某列

数据
```
$ cat data2.txt
5000:3:a1
200:1:a3
1000:4:a3
200:5:a2
900:6:a1
1000:2:a5

$ sort data2.txt
1000:2:a5
1000:4:a3
200:1:a3
200:5:a2
5000:3:a1
900:6:a1
```

按第二列排序`-k`
```
$ sort -t ':' -k2 data2.txt
200:1:a3
1000:2:a5
5000:3:a1
1000:4:a3
200:5:a2
900:6:a1
```

按第一个字段的第一个字符-第一个字段的第二个字符比较
```
$ sort -k1.1,1.2 data2.txt
1000:2:a5
1000:4:a3
200:1:a3
200:5:a2
5000:3:a1
900:6:a1
```



## 其他

测试一个文件是否已经被排过序`-C/-c`

```
# -C
$ sort -C data.txt
$ echo $?
1

$ sort -C data.sorted
$ echo $?
0

# -c
$ sort -c data.txt
sort: data.txt:3: disorder: 37  Go
$ sort -c data.sorted
$ echo $?
0
$ sort -c data.txt
sort: data.txt:3: disorder: 37  Go

```

合并两个已排序文件(不需要对合并后的文件进行再排序)`-m`
```
$ cat a b
1
2
3
2
4
9
$ sort -m a b
1
2
2
3
4
9
```

随机排序
```
$ cat b
2
4
9
$ sort -R b
9
4
2
$ sort -R b
2
4
9
```

