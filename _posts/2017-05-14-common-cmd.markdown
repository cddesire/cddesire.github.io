---
layout:     post
title:      "常用命令汇总"
date:       2017-05-13 13:30:00
author:     "Daniel"
header-img: "img/post-bg-tool.jpg"
tags:
    - Skill

---


####   中文乱码
``` sh
/etc/sysconfig/i18n，LANG="zh_CN.UTF-8"
```

####   查看环境变量
``` sh
env
```

####  查看当前用户的计划任务
``` sh
crontab -l
```

####   查看挂接的分区状态
``` sh
mount | column -t
```

####   查看所有分区
``` sh
fdisk   -l
```

####   查看各分区使用情况
``` sh
# df从整体上反映文件系统对节点和磁盘块的使用情况，du主要统计目录或文件所占磁盘空间的大小。

# 显示已用可用的磁盘空间
df -hT
# 显示当前目录磁盘使用统计
du -sh *
# 显示当前目录最大的10个文件或者文件夹
du -s * | sort -nr | head

```

####   查看所有交换分区
``` sh
swapon -s
```

####   查看内存使用量和交换区使用量
``` sh
free -m
```

####   查看内存总量
``` sh
grep    MemTotal     /proc/meminfo
```

####  查看空闲内存量
``` sh
grep   MemFree  /proc/meminfo
```

####  查看磁盘参数
``` sh
hdparm   -i    /dev/hda
```

####  查看系统负载
``` sh
cat /proc/loadavg
```

####  本机IP地址
``` sh
/sbin/ifconfig eth1 | grep Mask | awk '{print $2}'| cut -f2 -d:
```

####  修改IP地址
``` sh
ifconfig eth0 192.168.0.22 netmask 255.255.255.0
```

####  修改MAC地址
``` sh
ifconfig eth0 down; ifconfig eth0 hw ether 00:24:24:47:96:22; ifconfig eth0 up
```

####  查看网络端口
``` sh
netstat -tulnp
```

#### 列出所有网络状态：ESTABLISHED / TIME_WAIT / FIN_WAIT1 / FIN_WAIT2
``` sh
netstat -n | awk '/^tcp/ {++tt[$NF]} END {for (a in tt) print a, tt[a]}'
```

#### tcp 重传率
``` sh
#!/bin/bash
export PATH='/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin'
SHDIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"

netstat -s -t > /tmp/netstat_s 2>/dev/null

s_r=`cat /tmp/netstat_s | grep 'segments send out' | awk '{print $1}'`
s_re=`cat /tmp/netstat_s  | grep 'segments retransmited' | awk '{print $1}'`

[ -e ${SHDIR}/s_r ] || touch ${SHDIR}/s_r
[ -e ${SHDIR}/s_re ] || touch ${SHDIR}/s_re

l_s_r=`cat ${SHDIR}/s_r`
l_s_re=`cat ${SHDIR}/s_re`

echo $s_r > ${SHDIR}/s_r
echo $s_re > ${SHDIR}/s_re

tcp_re_rate=`echo "$s_r $s_re $l_s_r $l_s_re" | awk '{printf("%.2f",($2-$4)/($1-$3)*100)}'`
now=`date "+%Y-%m-%d %H:%M:%S"`
echo ${now},${tcp_re_rate}
```

####  统计代码行数
``` sh
# （ -name  中间有空格
find . -type f  \( -name "*.java" -o -name "*.scala" \) | xargs cat|grep -v -e ^$ -e ^\s*\/\/.*$|wc -l
```

####  同类型文件拷贝
``` sh
find some-dir -type f -name "*.txt" -exec cp \{\} new-dir \;
find some-dir -type f -name "*.txt" -print0 | xargs -0 cp --target-directory=new-dir
find some-dir -type f -name "*.txt" -print0 | xargs -I{} -0 cp -v {} /tmp/log-files
# 其中{}为参数列表标记，-0表示当文件名为空白行，那么当前的执行命令不起作用，-I表示用来替换初始参数。
# 删除7天前的日志
find some-dir -type f -mtime +7 -name  "*.log" | xargs rm -f
+N表示N天以前
-N表示N天以内
atime指access time，即文件被读取或者执行的时间
ctime即change time文件状态改变时间，指文件的i结点被修改的时间，如通过chmod修改文件属性
mtime即modify time，指文件内容被修改的时间
```

####  查看不间断增长的日志
``` sh
tail -f -n 5 my_server_log
```

#### 分析占用内存过高的进程
``` sh
top -cbo +%MEM | head -n 20 | tail -15
ps aux --sort -rss | head

ps -aux | sort -nrk3 | head -n 10
```

####  分析CPU过高的java线程
``` sh
ps -aux | sort -nrk3 | head -n 10

top -n1 -H | grep -m1 java
printf "%x" $PID
jstack $PID | grep -A500 $NID | grep -m1 "^$" -B 500
```

#### 更改文件所述用户和组
``` sh
chown changdong:xx_group project_dir -R
```

####  十进制与十六进制互转
``` sh
printf "%x\n" 4095
d2h(){echo "obase=16; $@"|bc}
printf "%d\n" 0xfff
h2d(){echo "ibase=16; $@"|bc}
```

#### 读取文件操作
``` sh
while read line
do
    echo $line
done < test.txt

cat test.txt | while read line
do
    echo $line
done

for line in $(cat  test.txt)
do
    echo $line
done

```

####  操作文件名中有空白字符文件
``` sh
find . -name "*.txt" -type f -print0 | while read -d $'\0' file; do cat "$file" >> merge.txt; done

find . -print0 | while read -d $'\0' file; do cp -v "$file" /tmp; done
```

####  找出所有文件内容包含MapReduce的文件
``` sh
find . -name "*.java" -print0 | xargs -0 grep -l "MapReduce"
# 当前目录下的所有文件查找
grep -RnsI --color=auto "MapReduce"  *
```

####  列出所有的maven依赖
``` sh
mvn dependency:resolve
mvn -o dependency:list | grep ":.*:.*:.*" | cut -d] -f2- | sed 's/:[a-z]*$//g' | sort -u
# cut中的-f m-n 表示显示第m栏到第n栏
# sed 's/要替换的字符串/新的字符串/g'
# sort -u 删除重复行
```

####  找出所有符号链接文件
``` sh
find ./ -type l -ls
```

####  如何找出大于10MB的tar.gz
``` sh
find / -type f -name *.tar.gz -size +10M -exec ls -l {} \;
```

####  显示PATH变量里的文件夹
``` sh
echo src::${PATH} | awk 'BEGIN{pwd=ENVIRON["PWD"];RS=":";FS="\n"}!$1{$1=pwd}$1!~/^\//{$1=pwd"/"$1}{print $1}'
```

####  批量制作目录的备份
``` sh
find -type -f -exec cp {} {}.bak \;
```

####  用sed方法删除数字、特殊字符等
``` sh
sed "s,\x1B\[[0-9;]*[a-zA-Z],,g"
```

####  删除结尾空白
``` sh
sed -i 's/[ \t]\+$//g' file.txt
# 移除最后一个字符
sed 's/.$//'
```

####  查看java进程
``` sh
jps -m -l -v
-m：输出传递给java进程主函数的参数
-l：输出主函数的完整路径
-v：显示传递给jvm的参数
```

####  查看gclog 间隔1秒打印一次，共打印10条
``` sh
jstat -gcutil 6148 1s 10
```

####  dump当前内存
``` sh
jmap -dump:format=b,file=./dumpfile1 6148
```

####  查看加载的对象统计
``` sh
jmap ‐histo 6148
```

####  查看两个目录中不同的文件
``` sh
diff -qr /dirA /dirB
```

#### 递归的修改当前目录内所有文件和目录的权限为777
``` sh
find . -print -exec chmod 777 {} \;
```

#### 删除文件
``` sh
# 空文件
find . -type f -size 0 -delete
# 删除空目录
find . -type d -empty -delete
# 删除7天前的文件
rm -rf `find -maxdepth 1 -mindepth 1 -mtime +7`
# 删除1年前的文件
find ./ -type f -mtime +365 -exec rm -f {} \;
```

#### 不重复的行
``` sh
awk '!($0 in a) {a[$0];print}' file
```

#### 命令赋值
``` sh
a=`ls`
echo "current path is $(pwd)"
echo "1+1=$((1+1))"
```

#### maven下载源码
``` sh
mvn dependency:sources -DincludeGroupIds=com.jcraft,org.testng -Dclassifier=sources
```

#### 排查jar冲突
``` sh
 mvn dependency:tree -Dverbose -Dincludes=com.alibaba:druid
```

#### tail 文件内容时，对每行输出增加一个时间戳
``` sh
tail -f file | while read; do echo -e "$(date +%T.%N) $REPLY"; done
```

####  tr 命令用于转换字符、删除字符和压缩重复的字符
``` sh
# 将文件中的小写字母转化为大写字母
cat filename | tr [:lower:] [:upper:]
cat filename | tr a-z A-Z
# 将文件中的{}替换为()
tr '{}' '()' < oldfile > newfile
# 替换空格为\t
echo "This Is Example" | tr [:space:] '\t'
# 如果有多个空格，用-s选项压缩重复空格
echo "This  Is     Example" | tr -s [:space:] '\t'
# 一行分成多行
echo "xxx=a&yyy=b&zzz=c" | tr "&" "\n"
# 多个\n字符压缩为单个\n，即空白行
cat filename | tr -s '\n'
# 打印PATH
echo $PATH | tr ':' '\n'
tr ':' '\n' <<< $PATH
# 删除指定字符，使用-d选项，如删除小写字母
echo "This Is Example" | tr -d a-z
# file: 1\n2\n3\n  $[ operation ]  执行算数运算
cat file | echo $[ $(tr '\n' '+') 0 ]
```

#### paste合并文件的行
``` sh
cat old
1,2
3,4
5,6
$ cat new
A,B
C,D
E,F
H,I
$ paste old new
1,2     A,B
3,4     C,D
5,6     E,F
        H,I
# 使用-d选项，作为分隔符
paste -d '---' old new
# 使用-s选项，每个文件合并为单一的一行
paste -d, -s old new
```

#### grep 使用
``` sh
# Grep OR
grep 'Tech\|Sales' employee.txt
grep -E 'Tech|Sales' employee.txt
egrep 'Tech|Sales' employee.txt
grep -e Tech -e Sales employee.txt
# Grep AND
grep -E 'Dev.*Tech' employee.txt
grep -E 'Manager.*Sales|Sales.*Manager' employee.txt
# Grep NOT
grep -v Sales employee.txt
# 查找包含"Manager"字串的文件
grep -lir "Manager" *     
```

####	Bash History Expansion
``` sh
# 执行历史上的一条命令，n为序号
$ !n
// 执行上一条命令
$ !!
$ !-1

# 使用关键字执行命令
$ !ps
ps -ef | grep http

# 替换前一条命令
$ ls /etc/cron.daily/logrotate

$ ^ls^cat^
cat /etc/cron.daily/logrotate

# 获取第一个参数（:^）
$ cp /etc/passwd /backup

$ ls -l !cp:^
ls -l /etc/passwd

$ ls -l !!:^
$ ls -l !^

# 获取最后参数（:$）
$ cp /etc/passwd /backup

$ ls -l !cp:$
ls -l /backup

$ls -l !!:$
$ls -l !$

# 获取第n个参数（:n）
$ tar cvfz /backup/home-dir-backup.tar.gz /home

$ ls -l !tar:2
ls -l /backup/home-dir-backup.tar.gz

# 获取所有参数（!*）
$ cp /etc/passwd /backup

$ ls -l !cp:*
ls -l /etc/passwd /backup

$ ls -l !*

// 获取部分参数(x-y)
$ tar cvf home-dir.tar john jason ramesh rita

$ ls -l !tar:3-5
ls -l john jason ramesh

$ ls -l !tar:2-$


# 去掉路径名称的尾部(:h)
$ ls -l /very/long/path/name/file-name.txt

$ ls -l !!:$:h
ls -l /very/long/path/name


# 去掉路径名称的头部(:t)
$ ls -l /very/long/path/name/file-name.txt

$ ls -l !!:$:t
ls -l file-name.txt

# 替换命令行的内容（:s）
$ cp /etc/password /backup/password.bak
$ !!:gs/password/passwd/
cp /etc/passwd /backup/passwd.bak

# 打印命令不执行（:p）
$ tar cvf home-dir.tar john jason ramesh rita

$ tar cvfz new-file.tar !tar:3-:p
tar cvfz new-file.tar john jason ramesh
```

#### xargs
``` sh
$ find . -name "*sh*"
# 合并为一行
$ find . -name "*bash*" | xargs
$ ls -1 *.sh | xargs

$ find . -name "*.sh" | xargs grep "ksh"

# 删除临时文件 -0 处理文件名中的空格
$ find /tmp -name "*.tmp" -print0 | xargs -0 rm

# 统计多个文件的行数
$ ls -1 *.sh | xargs wc -l

# 批量重命名
$ ls | xargs -t  -I  {} mv {} {}.old
# sed p 打印行  把base替换为ctr
$ find . -name "*job" -type f -depth 1| sed -e 'p;s/base/ctr/' | xargs -n2 mv

yarn application -list | grep "ctr-model" | awk -F " " '{print $1}' | xargs -I {} yarn application -kill {}
```

#### Shell内置变量
``` sh
$?:   表示sh命令的返回值
$$:    表示当前sh的pid.
$!:    最后一个放入后台作业的PID值.
$0:    表示脚本的名字.
$1--$9,${10}: 表示脚本的第一到九个参数,和第十个参数.
$#:    表示参数的个数.
$*,$@: 表示所有的参数.
```

#### 编码转换
``` sh
for i in `find . -name "*.java"`; do iconv -f gbk -t utf-8 $i > $i.new; done
find . -name "*.new" | sed 's/\(.*\).new$/mv "&" "\1"/' | sh
```

#### 搜索关键字杀死一组进程
``` sh
ps -ef|grep "keyword"|grep -v grep|cut -c 9-15|xargs kill -9
ps aux|grep "keyword"|grep -v grep |awk '{print $2}'|xargs kill -9
pgrep "keyword" | xargs kill -9
pkill "keyword"
```

#### 创建目录
``` sh
#创建目录，包括中间目录
mkdir -p long/directory/path
#创建目录，并进入
mkdir dir && cd $_
# 进入一个目录执行命令，并返回当前目录
(cd /tmp && ls)
```

#### 两个文件的交集和差集
``` sh
# file2 - file1
grep -Fxv -f file1 file2
cat file2 file1 file1 | sort | uniq -u
# 取两个文件的交集
grep -Fx -f file1 file2
cat file1 file2 | sort | uniq -d
# c 是 a 并 b
cat file1 file1 | sort | uniq
```

#### 切割文件
``` sh
split -l 1000 -d ip.txt ip_part_
```

#### 编译spark
``` sh
export MAVEN_OPTS="-Xmx2g -XX:ReservedCodeCacheSize=512m"  
# -Pxxx xxx为profile
mvn -Pyarn -Phadoop-2.7 -Dhadoop.version=2.7.0 -Phive -Dscala-2.12.2 -Phive-thriftserver -DskipTests clean package
# spark-streaming_2.11是streaming中定义的artifactId
mvn -pl :spark-streaming_2.11 clean install
mvn package -DskipTests -pl core
```

#### 输出内容格式化
``` sh
mount | column -t
cat /etc/passwd | column -t -s:
```

#### 编译proto文件
``` sh
protoc --proto_path=src --java_out=build/gen src/foo.proto
```

#### 脚本获取时间
``` sh
TODAY=`date '+%Y-%m-%d %H:%M:%S'`
YESTERDAY=`date --date="${TODAY} 1 day ago" +%F`
TWO_DAYS_AGO=`date --date="${TODAY} 2 days ago" +%F`
ONE_HOUR_AGO=`date --date="${TODAY} 1 hour ago" "+%Y-%m-%d/%Y-%m-%d.%H"`
TWO_HOUR_AGO=`date --date="${TODAY} 2 hours ago" "+%Y-%m-%d/%Y-%m-%d.%H"`
```

#### 不挂断地运行命令
``` sh
# no hang up
nohup command &
# nohup命令提交作业，输出定向到file.out。标准错误2重定向到标准输出中1，而标准输出又导入文件output里面。将标准错误重定向到标准输出的原因，是标准错误没有缓冲区，而stdout有。& 结尾的命令会在后台子sh中执行，该sh不会等待命令执行完成，返回状态0。
nohup bash command.sh >> a.log 2>&1 &
````

#### bash 拼接数组
``` sh
# arr_len=${#my_array[@]}
my_array=($(hadoop fs -ls /user/hour/ | grep 'XXX' | sort | tail -n 168))
arr_str="${my_array[@]}"
path=${arr_str// /,}
echo ${path}
```

#### bash 字符串切割数组
``` sh
a="one,two,three,four"
# 备份默认的分隔符
OLD_IFS="$IFS"
IFS=","
eval arr=($a)
IFS="$OLD_IFS"
for s in ${arr[@]}
do
  echo "$s"
done
```

#### bash 变量自增
``` sh
let i+=1;
((i++));
i=$[$i+1];
i=$(( $i + 1 ))
```

#### Bash中的变量
``` sh
# 命令替换来赋值
a=`echo Hello!`   
# 使用$(...)机制来进行变量赋值
arch=$(uname -m)

# Bash变量都是字符串
# 将"23"替换成"BB"
a=2334
b=${a/23/BB}
```

#### bash 条件判断


| - | test 与 `[ ]` |  `[[ ]]` |example |
|:--|:--|:--|:--|
|字符串比较|`=, ==, !=, -n, -z, <, >`|`=, ==, !=, -n, -z, <, >`|`if ! [ $str1 == $str2 ]; then ... ; fi`|
|整数比较|`-eq, -gt, -ge, -lt, -le, -ne`|`-eq, -gt, -ge, -lt, -le, -ne`|`if [ $1 -le 0 ]; then ... ; fi`|
|文件比较|`-e, -r, -w, -x, -f, -d, -L, -s, -b, -c, -t, -nt, -ot`|`-e, -r, -w, -x, -f, -d, -L, -s, -b, -c, -t, -nt, -ot`|`if [ -e /usr/local/localtime ]; then ... ; fi`|
|逻辑比较|`-a, -o, !`|`&&, \|\|, !`|`if [ $str1 == $str2 -a -n $str3 ]; then ... ; fi`|

``` {sh}
# -z ：判断 string 是否是空串
# -n ：判断 string 是否是非空串
# 必须用引号将字符串界定起来

STRING=""
if [ -z "$STRING" ]; then
    echo "STRING is empty"
fi

if [ -n "$STRING" ]; then
    echo "STRING is not empty"
fi

```

#### 条件分支
``` {sh}
# 命令执行成功，则其返回值为0，否则为非0

if [ $? -eq 0 ]; then
	echo "success"
elif [ $? -eq 1 ]; then
	echo "failed,code is 1"
else
	echo "other code"
fi

if [ 10 -gt 5 -o 10 -gt 4 ];then
	echo "10>5 or 10 >4"
fi
```

#### 循环
``` {sh}
#遍历输出脚本的参数
for i in $@; do
    echo $i
done

for ((i =0; i <10; i++)); do
    echo $i
done

for i in {1..5}; do
    echo "Welcome $i"
done

for i in {5..15..3}; do
    echo "number is $i"
done

for i in $(seq 1 10);do
    echo ${i}
done
```

#### 删除空白行
``` sh
tr -s '\n' < abc.txt
grep -v "^$" abc.txt
sed '/^$/d' abc.txt
sed '/./!d' abc.txt
awk '/./' abc.txt
```

#### 去除相同的行
``` sh
awk '!a[$0]++' file
```

#### 排序
``` sh
>>> cat test
chrom:index:forward
chr02:33:34
chr01:233:343454
chr01:10:3434
chr01:54353:998786
chr02:344:434
# 忽略第一行排序
-n, --numeric-sort  compare according to string numerical value
-k, --key=POS1[,POS2]  start a key at POS1, end it at POS2 (origin 1)
-t, --field-separator=SEP  use SEP instead of non-blank to blank transition
>>> head -1 test; tail -n +2 test | sort -t: -k1,1 -k2,2n
chrom:index:forward
chr01:10:3434
chr01:233:343454
chr01:54353:998786
chr02:33:34
chr02:344:434
```

#### 求和
``` sh
# 文件里面的数只有一列
awk '{s+=$0} END{print s}' data
paste -sd+ data | bc
echo $(tr -s '\n' '+' < data)0 | bc
echo $(sed 's/$/+/' data)0 |bc
```

#### 多个空格格式化成一个tab
``` sh
awk -v OFS="\t" '$1=$1' data
sed 's/[ \t]\+/\t/g' data
```

#### 合并连续的多行
``` sh
awk '{printf("%s%s", $0, (NR%3 ? "," : "\n"))}' data
# next相当于循环中continue的作用，next后面的语句将不再执行
awk 'NR%3{printf $0",";next;}1' data
````

#### brew
``` sh
# brew 安装
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bash_profile
brew doctor

brew install wget # 安装源码
brew info wget # 显示软件的各种信息
brew uninstall wget # 卸载软件
brew search wget # 模糊搜索brew
brew list # 列出本机通过brew安装的所有软件
brew update # 更新brew软件自身
brew upgrade wget # 更新安装过的软件
brew cleanup # 清除下载的各种缓存

# Homebrew替换源
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git
brew update

# Homebrew-bottles 
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```
[更多参考](http://zqpythonic.qiniucdn.com/data/20111223160257/index.html)

#### 数据补全跑批
``` {sh}
today=`date "+%Y-%m-%d %H"`
for ((i=0; i<=168; i++))
do
  ds=`date --date="${today} $i hour ago" "+%Y-%m-%d"`
  hh=`date --date="${today} $i hour ago" "+%H"`
  [[ ${ds} == '2017-10-13' ]] && break 2
  echo ${ds} ${hh}
done

dates=('2017-10-13' '2017-10-14')
hours=('00' '01' '02' '03' '04' '05' '06' '07' '08' '09' '10' '11' '12' '13' '14' '15' '16' '17' '18' '19' '20' '21' '22' '23')
for d in ${dates[@]}
do
   for h in ${hours[@]}
   do
      echo ${d} ${h}
   done
done

```

#### 花括号展开
``` sh
echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back
echo Number_{1..5}
Number_1 Number_2 Number_3 Number_4 Number_5
echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
echo {one,two,three}{1..5}

# 给 file 复制一个叫做 file.bak 的副本
cp /very/long/path/file{,.bak}

# 删除 file1.txt file3.txt file5.txt
rm file{1,3,5}.txt

# 将所有 .c 和 .cpp 为后缀的文件移入 src 文件夹
mv *.{c,cpp} src/

```

#### 获取文件名和后缀
``` sh
parameter expansion
# 替换第一个
${parameter/substring/replacement}
# 替换全部
${parameter//substring/replacement}
${parameter##remove_matching_prefix}
${parameter%%remove_matching_suffix}

${parameter:offset}
${parameter:offset:length}

fullfilename="/dir1/dir2/dir3/my.file.txt"
filename=$(basename "$fullfilename")
# 拿掉最后一条/及其左边的字符串
filename="${fullfilename##*/}"
# 拿掉第一条 / 及其左边的字符串
${fullfilename#*/}: dir1/dir2/dir3/my.file.txt
# 拿掉最后一个 . 及其右边的字符串
fname="${filename%.*}"
# 拿掉最后一个 . 及其左边的字符串
ext="${filename##*.}"
ext="${fullfilename##*.}"

# 提取最左边的 5 个字节
${fullfilename:0:5}
# 提取第 5 个字节右边的连续 5 个字节
${fullfilename:5:5}
```

#### dirname 脚本文件放置的目录
``` {sh}
# 如执行bash +x Documents/test.sh
echo "$(dirname `readlink -f -- $0`)"：Documents
echo "$(cd "$(dirname `readlink -f -- $0`)"; pwd)/find_spark_home.py"： /Users/changdong/Documents/find_spark_home.py
echo "$(cd "$(dirname `readlink -f -- $0`)"/..; pwd)"： /Users/changdong

FIND_SPARK_HOME_PYTHON_SCRIPT="$(cd "$(dirname `readlink -f -- $0`)"; pwd)/find_spark_home.py"

# -z ${SPARK_HOME}长度为0为真，或者-n 表示长度非0为真 -f 文件存在而且是一个普通文件
if [ ! -z "${SPARK_HOME}" ]; then
   exit 0
elif [ ! -f "$FIND_SPARK_HOME_PYTHON_SCRIPT" ]; then
  export SPARK_HOME="$(cd "$(dirname "$0")"/..; pwd)"
else
  if [[ -z "$PYSPARK_DRIVER_PYTHON" ]]; then
     PYSPARK_DRIVER_PYTHON="${PYSPARK_PYTHON:-"python"}"
  fi
  export SPARK_HOME=$($PYSPARK_DRIVER_PYTHON "$FIND_SPARK_HOME_PYTHON_SCRIPT")
fi

# 脚本名称和绝对路径
script=$(basename ${BASH_SOURCE:-$0})
basedir=$(cd $(readlink -f $(dirname ${BASH_SOURCE:-$0}));pwd)
```

#### vim sh批量注释
``` sh
添加注释
:10,100 s/^/#/g
取消注释
:10,100 s/^#//g
```

#### unix时间转换
``` sh
echo 1510798061 | awk '{print strftime("%c",$0)}'
Thu 16 Nov 2017 10:07:41 AM CST
echo 1510798061 | awk '{print strftime("%D",$0)}'
11/16/17
echo 1510798061 | awk '{print strftime("%T",$0)}'
10:07:41
echo 1510798061 | awk '{print strftime("%Y-%m-%d %T", $0)}'
2017-11-16 10:07:41
```

#### 查看打开的端口
``` sh
lsof -i -P | grep -i "listen" 查看打开的端口
-i, --ignore-case
```

#### spring boot run
``` sh
mvn -pl web -am spring-boot:run
-pl,--projects Comma-delimited list of specified reactor projects to build instead of all projects.
-am,--also-make
```

#### yarn节点状态
``` sh
yarn node -list -all
```

#### 数组包含contains
``` {sh}
array=(zero one two three four five)
if [[ "${array[@]}" =~ "one" ]]; then
    echo "111111"
fi
# ! 之后有空格
if [[ ! "${array[@]}" =~ "six" ]]; then
    echo "666666"
fi
```

#### 压缩多个大文件成多个zip文件
``` sh
find . -maxdepth 1 -type f -print0 | xargs -0 -n 3 bash -c 'zip $$.zip "$@"' none
```

#### 同步文件到服务器
``` sh
sshpass -p passwd rsync -av --progress --inplace --rsh='ssh -p88888' src dev@8.8.8.8:/home/dev/cd/project/
```

#### 批量作业等待
``` {sh}
unrelated_job &
for i in 1 2 3 4 5; do
  cmd & pids+=($!)
done
wait "${pids[@]}"   # Does not wait for unrelated_job, though

for i in 1 2 3 4 5; do
   cmd & pids+=($!)
done

for pid in "${pids[@]}"; do
   wait "$pid"
   # do something when a job completes
done
```

#### 创建软连接
``` sh
ln -s /data/changd/ ~/changd
# /data/changd/是实际存在的目录，~/changd不存在，将/data/changd/目录连接到用户目录下的changd
```

#### 文件或者目录大小排序
``` sh
# 该目录下所有子目录大小统计并排序
du -k | sort -rn
# 二级目录大小并排序
du -k --max-depth=2 | sort -rn
# 文件大小排序
ls -lS
```

#### 查看通配符文件是否存在
``` {sh}
files=$(ls test* 2> /dev/null | wc -l)
if [[ "$files" != "0" ]]; then
   echo "Exists"
else
    echo "None found."
fi
```

#### 解析json
``` sh
echo '{"hostname":"test","uid":"123"}' | python -c 'import json,sys;obj=json.load(sys.stdin);print obj.get("hostname")'

echo '{"hostname":"test","uid":"123"}'  | grep -Po '"uid":".*?"' | grep -Po '\d+' 

echo '{"hostname":"test","uid":"123"}'  | python -mjson.tool | grep hostname
# 提取value
echo '{"hostname":"test","uid":"123"}' | awk '{match($0, /hostname\":\"(\w+)\"/, a); print a[1]}'

# jq 命令
curl 'https://api.github.com/repos/stedolan/jq/commits?per_page=5'  | jq '.[] | {message: .commit.message, name: .commit.committer.name}'
# tr -d '"' 是将json中的引号去掉
pbpaste | jq '.[]|.id' | tr -d '"' | tr '\n' ','

```

#### 目录下的文件转换为array
``` sh
dirarr=(`ls ${prefix}*.text`)
echo ${dirlist[*]}

```

#### java 提交jar执行class
``` sh
${JAVA_HOME}/bin/java -Xbootclasspath/a:${PROJECT_HOME}/lib/jedis-2.9.0.jar:${PROJECT_HOME}/lib/commons-lang3-3.3.2.jar:${PROJECT_HOME}/lib/commons-io-2.4.jar -cp ${PROJECT_HOME}/lib/process-0.1.jar com.task.TagSimilaritySyncTask "${input_path}"
```

#### spark客户端启动
``` sh
spark-sql --num-executors 10 --executor-memory 8G --driver-memory 5G --driver-java-options "-Dlog4j.configuration=file:/home/opendev/changd/log4j.properties" -i changd/dong.sql

set spark.sql.shuffle.partitions=10;

# Set everything to be logged to the console
log4j.rootCategory=WARN, console
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.err
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n


# Settings to quiet third party logs that are too verbose
log4j.logger.org.eclipse.jetty=WARN
log4j.logger.org.eclipse.jetty.util.component.AbstractLifeCycle=ERROR
log4j.logger.org.apache.spark.repl.SparkIMain$exprTyper=WARN
log4j.logger.org.apache.spark.repl.SparkILoop$SparkILoopInterpreter=WARN

spark-sh --num-executors 10 --executor-memory 8G --driver-memory 5G
spark-sql --num-executors 24 --driver-memory 10G --executor-memory 10G -hivevar ds="2018-08-20" -f hive_fdt.sql
```

#### sed 插入空行 
``` sh
# 隔行插入空行  线先删除已有空行，然后添加一行
sed '/^$/d;G' data.txt

```

#### cpu 信息
``` sh
processor	: 31                 -> 逻辑核编号
vendor_id	: GenuineIntel
cpu family	: 6
model		: 85
model name	: Intel(R) Xeon(R) Silver 4110 CPU @ 2.10GHz
stepping	: 4
microcode	: 0x2000043
cpu MHz		: 2100.099
cache size	: 11264 KB
physical id	: 1                   -> 物理cpu编号
siblings	: 16                  -> 每个cpu的逻辑核数
core id		: 7
cpu cores	: 8                   -> 每个cpu的物理核数
apicid		: 31
initial apicid	: 31
fpu		: yes
fpu_exception	: yes
cpuid level	: 22
wp		: yes
bugs		:
bogomips	: 4201.60
clflush size	: 64
cache_alignment	: 64
address sizes	: 46 bits physical, 48 bits virtual

一台机器可能包含多块 CPU 芯片，多个 CPU 之间通过系统总线通信。
一块 CPU 芯片可能包含多个物理核，每个物理核就是一个运算单元（包括运算器、存储器等）。
超线程（Hyper-Threading）技术可以让一个物理核在单位时间内同时处理两个线程，变成两个逻辑核。但它不会拥有传统单核 2 倍的处理能力，也不可能提供完整的并行处理能力。

查看 CPU 个数
cat /proc/cpuinfo | grep 'physical id' | sort | uniq | wc -l
查看 CPU 物理核数
cat /proc/cpuinfo | grep 'cpu cores' | sort | uniq
查看 CPU 逻辑核数
cat /proc/cpuinfo | grep 'siblings' | sort | uniq
```

#### load average和cpu使用率
``` sh
cpu使用率程序对CPU时间片的占用情况，每100ms有40ms被程序占用，60ms空闲，使用率40%。
%Cpu(s):  0.5 us,  0.1 sy,  0.0 ni, 99.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%us：表示用户空间程序的cpu使用率（没有通过nice调度）
%sy：表示系统空间内核程序的cpu使用率
%ni：表示用户空间且通过nice调度过的程序的cpu使用率
%id：空闲cpu
%wa：cpu运行时在等待io的时间
%hi：cpu处理硬中断的数量
%si：cpu处理软中断的数量
%st：被虚拟机偷走的cpu

load average：在一段时间内cpu正在处理以及等待cpu处理的进程数之和的统计信息，反映了cpu使用队列的长度的统计信息。load average是基于内核的数量决定的，可以理解为每个内核load之和。按每个内核100%负载来算，4个内核，load average的值就是4。
load average: 0.19, 0.21, 0.19
表示最近1分钟，5分钟和15分钟的平均load

实际生产系统中，通用的经验法则是：平均负载 = 0.7 * CPU 逻辑核数，生产系统的 CPU 总使用率不要超过 70%
```

#### axel 多线程断点下载
``` sh
axel -n 8 url
```

#### tldr 常用命令例子
``` sh
tldr tar
```
 
#### && 简化 if else
``` {sh}
gzip -t a.tar.gz

if [[ 0 == $? ]]; then
    echo "good zip"
else
    echo "bad zip"
fi

gzip -t a.tar.gz && echo "good zip" || echo "bad zip"

[[ $hour = "06" && $minute = "00" ]] && ( 
    sh run_sh.sh etl_1.day.sh ${yesterday} &
    sh run_sh.sh etl_2.day.sh ${yesterday} & 
)&

# 成功则break
[[ $? -eq 0 ]] && break  
# 失败非0错误码
[[ $? -ne 0 ]] && exit 242 

```

#### Contains 子字符串
``` {sh}
string="My string"

if [[ $string == My* ]]; then
    echo "It's there!"
fi
```

#### 获取路径名和文件名
``` sh 
dirname '/home/lalor/a.txt'
/home/lalor
basename '/home/lalor/a.txt'
a.txt
``` 

#### 有用的alias
``` sh
alias ls="ls --color=auto"
alias ll="ls --color -al"
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias hcat='hadoop fs -cat'
alias hget='hadoop fs -get'
alias hls='hadoop fs -ls'
alias hmv='hadoop fs -mv'
alias htest='hadoop fs -test -e'
alias tree="ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/   /' -e 's/-/|/'" 
alias meminfo='free -m -l -t'
alias listen="lsof -P -i -n" 
alias port='netstat -tulanp'

mcd() { mkdir -p "$1"; cd "$1";} 
cls() { cd "$1"; ls;}
backup() { cp "$1"{,.bak};}
md5check() { md5sum "$1" | grep "$2";}
alias .="cd .."
alias ..="cd ../.."
alias ...="cd ../../.."
alias ....="cd ../../../.."
alias .....="cd ../../../../.."
alias netstatop='netstat -an|grep EST|awk '\''{print $5}'\''|awk -F : '\''{print $1}'\''|sort |uniq -c | sort -nr -k1 | head -n 20'
alias netstatotal='netstat -an|awk '\''/tcp/ {print $6}'\''|sort|uniq -c'
```

#### 多台机器查找
``` sh
multi.sh

#!/usr/bash

HOSTS=("XXXX" "XXXX" "XXXX")
USER=dev
COMMAND=$@
for HOSTS in ${HOSTS[@]}; do
   echo "== $HOSTS =="
   ssh $USER@$HOSTS "${COMMAND}"
done

bash multi.sh "grep 1848337580 logs/nginx/access.log"
```

#### redis 命令
``` sh
cat cmds.txt | redis-cli
redis-cli --stat -i 2
```

#### sh 检查
``` sh
set -o nounset
set -o errexit

# 脚本出错即停
set -e -o pipefail
```

#### 不可逆操作引用环境变量时
``` sh
# 如果data_path未定义, 报错.
rm -rf ${data_path:?"undefined 'data_path'"}
```

#### 死循环用colon(:)
``` {sh}

while : ;
do
  t=$(($RANDOM%10+1));
  echo sleep $t secs; 
  sleep $t;
done

```

#### 参数检查函数
``` {sh}
function check_argument() {
  local name=${1:?"missing 'name'"};shift
  local arg=${1:?"missing 'arg'"};shift
  local alternatives=${1:?"missing 'alternatives'"};shift

  if [ -z ${alternatives} ];then
    echo "ERROR: empty alternatives for '${name}', value='${arg}'" >&2
    exit 1
  fi
}

# e.g.
check_argument "service" "master" "master|regionserver"

script=$(basename ${BASH_SOURCE:-$0})
usage="FORMAT: ${script} <srcdir> <dstdir>"
srcdir=${1:?"undefined 'srcdir', ${usage}"};shift
dstdir=${1:?"undefined 'dstdir', ${usage}"};shift
```

#### crontab 使用vim
``` sh
export VISUAL=vim; crontab -e
```

#### wget下载整个FTP目录(含子目录)
``` sh
wget -nH -m ftp://192.168.19.1/tom/

-nH：不创建以主机名命名的目录
-m：下载所有子目录并且保留目录结构
```

#### flink 相关
``` sh
flink run --class com.dong.main.ActionStreamingMain rstream.jar
# 任务列表
flink list
# 取消任务
flink cancel 1a40c04639f3fd0d4b62fae9934fad99.
# 停止任务
flink stop 1a40c04639f3fd0d4b62fae9934fad99
```

#### git 相关
##### 回退回滚取消提交返回上一版本

1、git reset
git reset的作用是HEAD指向的位置改变为之前存在的某个版本。适用场景：如果想恢复到之前某个提交的版本，且那个版本之后提交的版本都不要了。
``` sh
git reset d5498d4
本地库HEAD指向的版本比远程库的要旧，强制push
git push -f origin master

工作区 － 暂存区(索引index) － 本地仓库
git reset --hard HEAD~1 三者的改变全都丢失，即代码的修改内容丢失
git reset --soft HEAD~1 回退到git commit之前，此时处在暂存区，即执行git add 命令后
git reset --mixed HEAD~1 回退到工作区，即git add之前
```

2、git revert 
git revert是用于“反做”某一个版本，以达到撤销该版本的修改的目的。适用场景：
如果想撤销之前的某一版本，但是又想保留该目标版本后面的版本，记录下这整个版本变动流程。
``` sh
93bd4ab为要撤销的版本号
git revert 93bd4ab
git push origin master
```

##### 多次commit合并

在本地仓库中提交了多次，提交push到公共仓库中之前，为了让提交记录更简洁明了，把最近4次的提交记录合并为一个完整的提交，然后再push到公共仓库。

``` sh
-i(interactive)模式展示，操作最近4次的提交
git rebase -i head~4

对于已经push的提交，先rebase，后git push -f 强制提交
```

##### git rebase VS git merge
相同点：将修改从一个分支合并到另一个分支。
`git merge feature master` master分支merge到feature上，feature分支中创建一个新的merge commit，它将两个分支的历史联系在一起。merge是一种非破坏性的操作，现有分支不会以任何方式被更改。如果master分支提交非常活跃，这可能会严重污染feature分支历史记录。
`git checkout feature; git rebase master` master分支rebase到feature分支上，会将整个feature分支移动到master分支的顶端，从而有效地整合了所有master分支上的提交。rebase通过为原始分支中的每个提交创建全新的commits来重写项目历史记录。rebase 的主要好处是可以获得更清晰的项目历史。git rebase的黄金法则是永远不要在公共分支上使用它。

##### 打tag
``` sh
git tag
git tag -a v1.2.4 -m "统计2"
git push origin v1.2.4

查看标签详细信息
git tag -ln

删除本地标签
git tag -d v1.2.4

删除远程标签
git push origin --delete tag v1.2.4

切回到某个标签
git checkout -b branch_name tag_name

```

##### 清理分支
``` sh
删除已经合并到 master 的分支
git branch --merged master | grep -v '^\*\|  master' | xargs -n 1 git branch -d

查看远程分支和本地分支的对应关系
git remote show origin

远程删除了分支本地也删除
git remote prune origin

```

##### 拉取更新子模块
``` sh
git submodule update --init --recursive
```

##### 拉取远程分支
``` sh
// --track is shorthand for git checkout -b [branch] [remotename]/[branch]
git fetch --all
git checkout --track origin/daves
```

#### log
``` {sh}
function log_info()
{
    now=`date "+%Y-%m-%d %H:%M:%S"`
    echo -e "[${now}][INFO] $1 $2"
}

function log_error()
{
    usage="log_error <task> <message>"
    local task=${1:?"undefined 'task': ${usage}"};shift
    local message=${1:?"undefined 'message': ${usage}"};shift
    [[ -z ${message} ]] && (
        echo "ERROR: empty message for '${task}', message='${message}'" >&2
        exit 1
    )
    now=$(date "+%Y-%m-%d %H:%M:%S")
    echo -e "[${now}][ERROR] $1 $2"
    message="[hadoopclient2][ERROR]$1$2"
    url="http://127.0.0.1:1234/warning?group=123"
    curl -G -v "${url}" --data-urlencode "&msg=${message}"
    exit -1
}
```

#### docker
##### docker 镜像
``` sh
列出本地主机上的镜像
docker images

下载镜像
docker pull ubuntu

删除镜像
dokcer rmi -f b655e15959b7
docker rmi -f ${docker images -qa}

根据Dockerfile构建镜像
从当前路径的Dockerfile构建 镜像名称为dongdong，版本为v1.0
docker build --no-cache . -f Dockerfile -t dong_fedora:1.0.0\

Dockerfile
FROM fedora:29
RUN dnf install -y \
    which make libtool cmake clang gcc-gfortran \
    redhat-rpm-config gperftools-devel libatomic \
    dumb-init lldb gdb python2-pip python2-devel \
    git libasan mariadb-connector-c-devel supervisor \
    htop grep procps-ng iproute vim hostname \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "alias ll='ls -l'" >> /root/.bashrc
ENV LANG=C.UTF-8 CC=gcc CXX=g++ GIT_SSH_COMMAND='/usr/bin/ssh -o StrictHostKeyChecking=no -i /keys/id_rsa' PEGASUS_ENV=TEST
# ADD your_key /keys/id_rsa
# RUN export GIT_SSH_COMMAND='/usr/bin/ssh -o StrictHostKeyChecking=no -i /keys/id_rsa'
ENV DATA_GROUP_ID=1
CMD ["/usr/bin/python2", "/usr/bin/supervisord", "-c", "/etc/supervisord.conf", "-n"]

```
##### docker 容器
``` sh
创建容器
docker run -dit --restart unless-stopped --name dongdong -v /data/dongdong/workspace:/workspace -v /data/dongdong/data:/data/src centos

-i 允许对容器交互 -t 在新容器内指定一个终端 -d 后台运行
-v 目录挂载 主机目录:docker容器目录
-name 容器名字

容器列表
docker ps 

启动停止容器
docker start[stop,restar,kill] dongdong

删除容器
docker stop dongdong
docker rm dongdong

进入容器
docker exec -it --env COLUMNS=`tput cols` --env LINES=`tput lines` dongdong /bin/bash 

```

#### hdfs文件查看
``` sh
hadoop fs -cat /Data/Logs/2018-08-22/2018-08-22_log.lzo | lzop -dc

hadoop fs -cat /shining/temp.txt.gz | gzip -d 
hadoop fs -cat /shining/temp.txt.gz | zcat

hadoop fs -cat /temp/b.bz2 | bzip2 -d
```

