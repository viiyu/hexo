title: a sed feature of delimiter
date: 2016-04-11 23:18:50
categories: Practice
tags:
    - linux
    - shell
    - sed
---
## 问题
在一次使用sed结合shell参数对文本进行替换时，会报`bad flag in substitute command`之类错误。我只是简单的把一个文本中的目标字符串替换成bash脚本里定义的变量，写出来大概是这样一句：

```bash
sed "s/path/${local_path}/g" tmp.log
```

其中，后来用别的方法尝试了几次，发现是因为在变量`${local_path}`中包含了'/'。

## 原因
在sed的info里可以查到关于's'命令中分隔符的描述：

```
The syntax of the `s' (as in substitute) command is
`s/REGEXP/REPLACEMENT/FLAGS'.  The `/' characters may be uniformly
replaced by any other single character within any given `s' command.
The `/' character (or whatever other character is used in its stead)
can appear in the REGEXP or REPLACEMENT only if it is preceded by a `\'
character.
```

这里已经说明的很清晰了，与s命令中分隔符相同的字符只能以转义的形式出现在匹配字符串或者替换字符串中。而跟在s后的任意单字节字符会作为分隔符。

## 其他：关于单引号和双引号
* 单引号是强转义，将shell变量用在单引号里不会被当作一个变量，而会被当成文本
* 双引号反之