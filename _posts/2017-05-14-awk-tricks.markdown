---
layout:     post
title:      "awk 使用技巧"
date:       2017-05-13 16:30:00
author:     "Daniel"
header-img: "img/post-bg-tool.jpg"
tags:
    - Awk

---

####	初始入门
```	shell
cat awk.txt
Item1,200
Item2,500
Item3,900
Item2,800
Item4,500
Item1,400
Item2,200
Item3,700
Item4,800
Item1,600
```

-	**第二列求和**
```	shell
awk -F","'{x+=$2}END{print x}' awk.txt
5600
```

-	**对某个Item求和**
```	shell
awk -F,'$1=="Item1"{x+=$2;}END{print x}' awk.txt
1200
```

-	**去重**
```	shell
awk -F, '{a[$1];}END{for (i in a)print i;}' awk.txt
Item1
Item2
Item3
Item4
```

-	**分组求和sum**
```	shell
awk -F,'{a[$1]+=$2;}END{for(i in a)print i", "a[i];}' awk.txt
Item1, 1200
Item2, 1500
Item3, 1600
Item4, 1300
```

-	**分组max**
```	shell
awk -F, '{if (a[$1] < $2)a[$1]=$2;}END{for(i in a){print i,a[i];}}' OFS=, awk.txt
Item1,600
Item2,800
Item3,900
Item4,800
```
-	**分组count**
```	shell
awk -F, '{a[$1]++;}END{for(i in a)print i, a[i];}' awk.txt
Item1 3
Item2 3
Item3 2
Item4 2
```

-	**分组拼接**
```	shell
awk -F,'{if(a[$1])a[$1]=a[$1]" "$2;else a[$1]=$2;}END{for(i in a) printi,a[i]}' OFS='\t' awk.txt
Item1       200  400  600
Item2       500  800 200
Item3       900  700
Item4       500  800
```
-	**取第二行开始，每隔3行的数据**
```	shell
awk 'BEGIN{i=2} {if(NR==i) {print substr($0, 2, length($0)-2); i+=3}}' ok.txt
Item2,500
Item4,500
Item3,700
```

####	其他用法

-	字符串函数

|函数|描述|
|:--|:--|
|gsub(r,s)|在整个$0中用s替代r|
|gsub(r,s,t)|在整个t中用s替代r|
|index(s,t)|返回s中字符串t的第一位置|
|match(s,r)|测试s是否包含匹配r的字符串|
|split(s,a,t)|在t上将s分成序列a|
|sprint(fmt,exp)|返回经fmt格式化后的exp|
|sub(r,s)|用$0中最左边最长的子串代替s|
|length(s)|返回s长度|
|substr(s,p)|返回字符串s中从p开始的后缀部分|
|substr(s,p,n)|返回字符串s中从p开始长度为n的后缀部分|


-	**特定行全部替换gsub**
``` shell
awk '/baz/ { gsub(/foo/, "bar") }; { print }'
awk '!/baz/ { gsub(/foo/, "bar") }; { print }'
awk '{ gsub(/scarlet|ruby|puce/, "red"); print}'
```

-	**删除每行起始的空白**
```	shell 
awk '{sub(/^[ \t]+/, ""); print }'
```

-	**删除每行结尾的空白**
```	shell
awk '{sub(/[ \t]+$/, ""); print }'
```

-	**删除每行收尾的空白**
```	shell
# gsub(regular expression, subsitution string, target string)
awk '{gsub(/^[ \t]+|[ \t]+$/, ""); print }'
```

-	**删除空白行**
```	shell	
awk 'NF'
awk 'NF > 0'
awk '!/^$/'
awk '/./'
```

-	**删除每行的第二个字段**
```	shell
awk '{ $2 = ""; print }'
```

-	**每隔n行插入空行**
```	shell
awk '{ if ((NR % 5) == 0) printf("\n"); print; }'
```

-	**每5行合并为一行**
``` shell
# ORS - Output Record Separator 
awk 'ORS=NR%5?",":"\n"'
```

-	**抽取指定范围的行**
```	shell
awk 'NR >= 10 && NR <= 20'
```

-	**替换分隔符**
```	shell
awk '$1=$1' FS="----" OFS=","
awk 'gsub("----",",")'
```

-	**找出匹配的子串**
```	shell
echo "abcdef" | awk '{match($0,/b(.*)e/); print substr($0,RSTART)}'  #bcdef
echo "abcdef" | awk '{match($0,/b(.*)e/,a); print a[0]}'  #bcde
echo "abcdef" | awk '{match($0,/b(.*)e/,a); print a[1]}'  #cd
echo "abcdef" | gawk 'match($0, /b(.*)e/, a) {print a[1]}'  #cd
echo "xxx=a&yyy=b&zzz=c" | awk '{match($0,"yyy=([^&]+)",a)}END{print a[1]}'

echo "this is chen,and wang,not wan" | awk '{match($0, /.+is([^,]+).+not(.+)/, a); print a[1], a[2]}' # chen  wan
```

-	**写入多个文件**
```	shell
awk '{print >> ("filename"$1)}'
```

-	**计算调用方法耗时**
``` shell
tail -100000 performance.log | awk -F '-' '{print substr($2, 2, length($2) - 3)}' | awk '/^com/ {print $0}' | awk -F '=' '{a[$1]+=$2; b[$1]++;}END{for(i in a){print i, b[i], a[i] / b[i];}}' | sort -n -k 3 -r
```

- **过滤**
``` shell
awk '/l*c/{print}' /etc/localhost
```

- **两个文件join**

   	**a.txt**

	   	111 org
	   	222 edu
	   	333 gov 
	   	444 net

   	**b.txt**

	   	111 Tom Green.
	   	444 Ani Teen.
	   	888 Dany Cross. 

	**结果要求**

		111--org--Tom--Green.
		444--net--Ani--Teen.

	```shell
	awk -v OFS='--' 'NR==FNR {a[$1]=$2}; NR!=FNR && a[$1]{print $1, a[$1], $2, $3}' a.txt b.txt 
	```
	> FNR,与NR功用类似,不同的是awk每打开一个新文件,FNR便从0重新累计。

- **split**
``` shell
# split (string, array, field separator)
time="12:34:56"
out=`echo $time | awk '{split($0,a,":");print a[1],a[2],a[3]}'`
echo $out
```






