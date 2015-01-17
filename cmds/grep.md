# grep

文本搜索工具, 搜索指定文件中包含给定字符串或者单词的行, 支持正则表达式和通配符

语法:

```
grep "match_pattern" file1 file2
```
获取帮助`man grep`

测试文件(\t分隔)
```
131 Python
54  JavaScript
37  Go
22  CSS
18  C
12  VimL
11  Shell
11  Ruby
10  C++
5   Java
3   PHP
3   Objective-C
3   Scala
2   CoffeeScript
2   TeX
1   Makefile
1   Haskell
1   Objective-C++
1   C#
1   Erlang
1   Perl
1   Swift
```

## 简单用法

基本
```
$ grep Java data.txt
54  JavaScript
5   Java
```

输出匹配行号`-n`(--line-number)
```
$ grep -n Java data.txt
2:54    JavaScript
10:5    Java
```

使用颜色标注命中单词`—color=always/never/auto`
```
$ grep --color=auto Java data.txt
54  JavaScript
5   Java
```

忽略大小写`-i`(Ignore)

```
$ grep java data.txt
$ grep -i java data.txt
54  JavaScript
5   Java
```

只输出匹配到的单词`-o`(--only-matching)
```
$ grep -o Java data.txt
Java
Java
```

输出匹配外的所有行`-v`(--invert-match)
```
$ grep -v Java data.txt
131 Python
37  Go
22  CSS
......
```

统计匹配到的行数`-c`(--count)
```
$ grep -c Java data.txt
2
```

仅匹配N次`-m`(--max-count=NUM)
```
$ grep -m 1 Java data.txt
54  JavaScript
```


递归搜索目录`-R`(--recursive)
```
$ grep -R Java test
test/data.txt:54    JavaScript
test/data.txt:5 Java
```

搜索二进制文件`-a`(--text)
```
$ grep CONFIG config.pyc
Binary file config.pyc matches


$ grep -a CONFIG config.pyc
8:SECRET_KEYtTruetCONFIG_OPTIONSt
```



## 复合用法

使用正则(关于-E/-e区别, 这里不做阐述)

```
$ grep  -E 'J+' data.txt
54  JavaScript
5   Java
```

匹配多个pattern`-e`

```
$ grep -e "Java" -e "Shell" data.txt
54  JavaScript
11  Shell
5   Java
```

只包含/不包含某类文件`--include/--exclude/--exclude-dir`
```
$ grep Shell *
data.txt:11 Shell
include.py:11   Shell
noinclude.log:11    Shell

$ grep --include *.log Shell *
noinclude.log:11    Shell

$ grep --exclude *.log Shell *
data.txt:11 Shell
include.py:11   Shell

# 不包含某个目录
$ grep -R Shell *
data.txt:11 Shell
include.py:11   Shell
noinclude.log:11    Shell
text/noinclude.log:11   Shell

$ grep -R  --exclude-dir text Shell *
data.txt:11 Shell
include.py:11   Shell
noinclude.log:11    Shell

```

打印匹配行之前/之后/附近的N行`-A/-B/-C`(--after-context=NUM, --before-context=NUM, --context=NUM)

```
# 之后1行
$ grep -A 1 Java data.txt
54  JavaScript
37  Go
--
5   Java
3   PHP

# 之前1行
$ grep -B 1 Java data.txt
131 Python
54  JavaScript
--
10  C++
5   Java

# 前后各1行
$ grep -C 1 Java data.txt
131 Python
54  JavaScript
37  Go
--
10  C++
5   Java
3   PHP

```

是否输出文件名`-h`(--no-filename)
```
# 默认输出文件名
$ grep Shell *
data.txt:11 Shell
include.py:11   Shell
noinclude.log:11    Shell

# 不要文件名
$ grep -h Shell *
11  Shell
11  Shell
11  Shell
```



得到命中/未命中的文件列表`-L`(--files-without-match)

```
$ grep -l Java *
data.txt
include.py
noinclude.log

# 输出不匹配的文件列表
$ grep -L Shell *
text
```

## 其他用法

静默输出用于条件判断`-q`

```
$ grep -q Shell data.txt
$ echo $?
0
$ grep -q Shell2 data.txt
$ echo $?
1
```

将pattern放在文件中,批量命中
```
$ cat pattern_file
Java
Shell

$ grep -f pattern_file data.txt
54  JavaScript
11  Shell
5   Java

```

grep动态文件, 使用行缓冲(只输出复合规则的变更)
```
tail -f file.txt | grep something  --line-buffered
```



