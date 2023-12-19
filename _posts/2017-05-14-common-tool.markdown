---
layout:     post
title:      "常用工具及快捷键"
date:       2017-05-13 16:50:00
author:     "Daniel"
header-img: "img/post-bg-tool.jpg"
tags:
    - Tool

---

### Latex

-	[Templates and Sample Files](http://www.math.duke.edu/computing/tex/templates.html)
-	[Getting Started with LaTeX](http://www.maths.tcd.ie/~dwilkins/LaTeXPrimer/)
-	[Online tutorials on LaTeX](http://www.tug.org.in/tutorials.html) 
-	[Not so short](http://mirror.neu.edu.cn/CTAN/info/lshort/english/lshort.pdf)
-	[LaTeX-Online-Help](http://www.tex.ac.uk/cgi-bin/texfaq2html?introduction=yes)
-	[在线编辑LaTex](http://www.codecogs.com/latex/eqneditor.php)
-   Mathpix Snoipping Tool


### MAC
-	删除 		反向删除：Fn+Delete  删除一整个词：Option+Delete
-	全屏			Command-Control-F
-	Home键		Command+左方向
-	End键		Command+右方向
-	截屏			Command-Control-Shift-4  或者印象笔记圈点
-	显示桌面		Command+F3
-   axel  并发下载
-   jq    json
-   tldr  short man
-   wget  download
-   jvm-mon  jvm


### Sublime
-	选择所有相同的内容			Alt+F3/Command+Ctrl+G
-	选择下一个相同的内容		Command+D
-	跳过下一个相同的内容		先Command+K，然后Command+D
-	连续选择下一行				Command+L
-	删除行 					Control+Shift+K
-   选择括号内的内容 			Control+Shift+M
-	光标移动至括号末尾			Control+M
-	大写						Ctrl+K+U/Command+K+U
-	小写 					Ctrl+K+L/Command+K+L
-	替换as之前的所有字符串		Find What: [ \t]+\n  Replace With: \n
-	替换--之后的字符			Find What: --(.+)  Replace With: (nothing, leave in blank)
-	trim字符前后的空格			Find What: [ \t]+  Replace With: (nothing, leave in blank)
-	删除空白行				Find What: ^\s* 或者 ^\n  Replace With: (nothing, leave in blank)


### Sublime 常用插件
-	BracketHighlighter
-	Alignment
-	PackageResourceViewer 设置主题参数

### Sublime 配置
``` json
{
	"color_scheme": "Packages/Color Scheme - Default/Monokai.sublime-color-scheme",
	"font_face": "Source Code Pro",
	"font_size": 14,
	"ignored_packages":
	[
		"Vintage"
	],
	"line_padding_bottom": 2,
	"line_padding_top": 3,
	"theme": "Default.sublime-theme",
	"tab_size": 4,
        "translate_tabs_to_spaces": true,
        "expand_tabs_on_save": true
}

```


### JetBrains激活
-	http://xidea.online
-	http://idea.iteblog.com/key.php

### java 工程
-  https://start.aliyun.com/bootstrap.html


### IDEA 常用插件
-   ignore
-   maven helper
-   lombok
-   p3c
-   findbugs
-   mavenhelper
-   GenerateAllSetter


### IDEA
-  重构一切      Ctrl+Shift+Alt+T
-  自我修复      Alt+Enter / Option+Enter
-  发号施令      Ctrl+Shift+A / Command+Shift+A
-  选你所想      Ctrl+W   Ctrl+Shift
-  切来切去      Ctrl+Tab  / Control+Tab
-  代码生成      fori/itar/sout/psvm+Tab   user.for+Tab   user.getBirthday().var+Tab 
                         I for   psf psfi  ifn inn

#### 动作类
-  快速修复代码	Option+Enter
-  格式化代码     Command+Option+L
-  优化import	Control+Option+O
-  删除当前行		Command+Delete
-  拼接成一行		Control+Shift+J
-  查看定义		Command+Y
-  生成override  Command+N
-  注释			Command+/ 注释（//）Command+Option+/ 注释（/*...*/）
-  大小写			Command+Shift+U
-  定位行			Command+L
-  提取方法       Command+Option+M
-  重构代码       Control+T


#### 搜索类
-  查询任何		Double Shift
-  文件内查找     Command+F
-  文件内替换		Command+R
-  全局查找		Command+Shift+F
-  查找类文件		Command+O  / Command+Double O 查找jar包中的类
-  查找所有文件	Command+Shift+F
-  调用层次		Command+Option+H 查看方法的调用层次



### Cmder
-  中文乱码      set LANG=zh_CN.UTF8 或者命令 chcp 65001


### VSCode 
- 打开快捷键一览表 Ctrl(Command) + K + S
- 切换工作区 Ctrl(Command) + R
- 跳转到指定行 Ctrl(Control) + G
- 转到文件 Ctrl(Command) + P
- 选择内容格式化  Ctrl(Command) + K + F
- 转到定义  Fn + F12
- 变量重命名 Fn + F2
- 列出函数名 Ctrl + Shift + O  Ctrl + P + @
- 删除整行	Ctrl + Shift + K
- 查找类  Ctrl + T   Ctrl + P + #


### VI
#### 插入
-	i，I：插入，小写插入到光标位置，大写插入到一行开始 
-	a，A：追加： 小写在光标位置追加，大写行尾追加 
-	o，O：插入空白行，小写在当前行下方，大写在当前行上方

#### 光标移动

|命令|说明|
|:--|:--|
|h，j，k，l|上下左右移动|
|w，W| 向前跳过一个单词，停留在单词头部，小写考虑拼写，大写不考虑|
|e，E|向前跳过一个单词，停留在单词尾部|
|b，B|回退跳过一个单词|
|^ |一行开始 `shift + 6`|
|$|一行结束 `shift + 4`|
|5G|一次跳过5行|
|ctrl+f ctrl+b|往前翻、往后翻|
|N + %|跳转到文件的N%处|
|H |跳转到屏幕的第一行  |
|M |跳转到屏幕的中间一行  |
|L |跳转屏幕的最后一行|

#### 剪切复制（Cut and Paste）

|命令|说明|
|:--|:--|
|yy|（yank、copy） 复制一行|
|2yy |复制2行|
|yw|复制一个单词|
|y$|复制到行尾部|
|p，P|（put、paste）粘贴|
|dd|删除一行|
|dw|删除光标所在单词|
|x|删除一个字符|

#### 替换

|命令|说明|
|:--|:--|
|`:s/aa/bb/g`|将光标所在行出现的所有包含 aa 的字符串中的 aa 替换为 bb|
|`:s/\<aa\>/bb/g`|	  将光标所在行出现的所有 aa 替换为 bb, 仅替换 aa 这个单词|
|`:%s/aa/bb/g`|	  将文档中出现的所有包含 aa 的字符串中的 aa 替换为 bb|
|`:12,23s/aa/bb/g`|  将从12行到23行中出现的所有包含 aa 的字符串中的 aa 替换为 bb|
|`:12,23s/^/#/`|	  将从12行到23行的行首加入 # 字符|
|`:10,23s#^#//#g`|	  将从12行到23行的行首加入 // 字符|
|`:10,23s#^//##g`|	  将从12行到23行的行首删除 // 字符|
|`:%s= *$==`|	      将所有行尾多余的空格删除|
|`:g/^\s*$/d`|	      将所有不包含字符(空格也不包含)的空行删除|
|`:%s/\\/\//g`|		全文\替换为/ （特殊字符：^、$、*、/、\和.都需要转义，前面加上\）|

### vi 配置
``` shell
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8

syntax on
set number
set showmatch
set tabstop=4
set shiftwidth=4
set autoindent
set laststatus=2
set ruler
autocmd BufWritePost $MYVIMRC source $MYVIMRC
set t_Co=256
set showmode
set showcmd
filetype indent on
set textwidth=80
set wrap
set linebreak
set nobackup
set noswapfile

set nocompatible
set backspace=2
set nu
```

### BASH

|快捷键|描述|
|:--|:--|
|`Control+R`|搜索命令行历史记录|
|`Control+W`|删除键入的最后一个单词|
|`Control+U`|删除行内光标所在位置之前的内容|
|`Control+K`|删除光标至行尾的所有内容|
|`Control+A`|光标移至行首|
|`Control+E`|光标移至行尾|
|`Option+方向键`|以单词为单位移动光标|

### Python 安装
-	sudo python3 -m pip install --upgrade tensorflow --index-url http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
-	python3 -m pip install -U synonyms
-   临时使用 pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
-   默认配置 pip install pip -U
            pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
-   配置镜像
``` conf
[global]
timeout = 6000
index-url = http://pypi.douban.com/simple/ 
[install]
use-mirrors = true
mirrors = http://pypi.douban.com/simple/ 
trusted-host = pypi.douban.com
```
-  conda 常用命令
``` shell
1、工作环境
conda env list #或 conda info --env

2、创建工作环境
#1.创建指定Python版本的环境
conda create --name env_name python=3.9
#2.创建指定某些包的环境
conda create --name env_name numpy scipy
#3. 1与2结合
conda create --name env_name python=3.9 numpy scipy

3、进入和退出工作环境
# 进入(或切换到)py9t工作环境
activate py39t
# 退出工作环境
conda deactivate

4、导出工作环境配置
#1.导出工作环境到 yml 文件
conda env export > py39t.yml
#2.导入 yml 文件工作环境
conda env create -f py39t.yml

5、更新工作环境
conda update conda
conda update --all
```

-  conda安装找不到安装
``` python
anaconda search -t conda mleap
anaconda show Anaconda-Extras/mleap
conda install --channel https://conda.anaconda.org/Anaconda-Extras mleap
```

- 格式化python代码
``` python
python3 -m black dag.py
python3 -m black --check --diff dag.py
```

### Excel 常用公式
``` code
A列中是否包含B 
=IF(COUNTIF(A:A,B1)=0,"无相同","有相同")

返回A1单元格的内容再B列里第n行存在
=MATCH(A1,B:B,)
```

### Excel VLOOKUP
##### 基础单条件查找
![vlookup1](/img/in-post/common-tool/vlookup1.jpg)

##### 反向查找
![vlookup2](/img/in-post/common-tool/vlookup2.jpg)

##### 多条件查询
![vlookup3](/img/in-post/common-tool/vlookup3.jpg)

##### 查询返回多列
![vlookup4](/img/in-post/common-tool/vlookup4.jpg)

### Excel 行列转化
#### 列转多行
![offset1](/img/in-post/common-tool/offset1.jpg)
#### 行转多列
![offset2](/img/in-post/common-tool/offset2.jpg)

### Excel 常用技巧
-	快速选取资料 command + shift + 上下左右方向
-	转置  复制右击选择性粘贴，转置
-	条件定位  F5 或者 ctrl + G
-	批量填充选中单元格  输入第一个，然后 ctrl+enter 
-	批量填充空格  F5条件定位选择空值，输入要填充的内容，ctrl+enter 填充全部
-	替换 ctrl + H


### Excel 组合图做法
![combination_chart](/img/in-post/common-tool/combination_chart.jpg)

-	step1：选择数据，插入粗壮柱形图；
-	step2：添加主标轴、次坐标轴标题：选择主坐标轴 -> 图标设计 -> 添加图标元素 -> 坐标轴标题，输入标题之后拖到坐标轴上方，设置坐标轴选项 -> 对齐方式 -> 文字方向 -> 水平；
-	step3：图例上方显示，图例选项 -> 边框 -> 实线；删除网格线；
-	step4：设置刻度线，坐标轴选项 -> 刻度线 -> 内部；坐标轴选项 -> 线条 -> 颜色 -> 黑色；
-	step5：坐标轴数值间距：坐标轴选项 -> 坐标轴选项 -> 单位；坐标轴选项 -> 数字 -> 小数位数；
-	step6：数据标签：右击 -> 添加数据标签；选择标签 -> 标签选项 -> 标签位置；
-	step7：线条粗细：右击 -> 设置数据系列 -> 线条 -> 宽度；右击 -> 设置数据系列 -> 标记 -> 标记选项 -> 内置；
-	step8：柱形图间距：选择柱子 -> 系列 -> 选项系列绘制在 -> 间隙宽度；


### PATH HOME
``` code
# MAVEN
export M2_HOME=/Users/changdong/Documents/software/apache-maven-3.5.2
PATH=$PATH:$M2_HOME/bin

# JAVA
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# SCALA
export SCALA_HOME=/Users/changdong/Documents/software/scala-2.12.4
PATH=$PATH:$SCALA_HOME/bin

export PATH="/usr/local/bin:$PATH"

# Google
export GOOGLE_API_KEY="no"
export GOOGLE_DEFAULT_CLIENT_ID="no"
export GOOGLE_DEFAULT_CLIENT_SECRET="no"


# alias
alias ls='ls -lAt'
alias ll='ls  -al'

# brew
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles


# Iterm2
# enables colorin the terminal bash shell export
export CLICOLOR=1
# sets up thecolor scheme for list export
export LSCOLORS=gxfxcxdxbxegedabagacad
# enables colorfor iTerm
export TERM=xterm-color

find_git_branch () {
	local dir=. head
	until [ "$dir" -ef / ]; do
		if [ -f "$dir/.git/HEAD" ]; then
			head=$(< "$dir/.git/HEAD")
			if [[ $head = ref:\ refs/heads/* ]]; then
				git_branch=" (${head#*/*/})"
			elif [[ $head != '' ]]; then
				git_branch=" → (detached)"
			else
				git_branch=" → (unknow)"
			fi
			return
		fi
		dir="../$dir"
	done
	git_branch=''
}

PROMPT_COMMAND="find_git_branch; $PROMPT_COMMAND"
black=$'\[\e[1;30m\]'
red=$'\[\e[1;31m\]'
green=$'\[\e[1;32m\]'
yellow=$'\[\e[1;33m\]'
blue=$'\[\e[1;34m\]'
magenta=$'\[\e[1;35m\]'
cyan=$'\[\e[1;36m\]'
white=$'\[\e[1;37m\]'
normal=$'\[\e[m\]'
PS1="$white$white@$green\h$white:$cyan\W$yellow\$git_branch$white\$ $normal"


```

### item免密登录
``` sh
# 在.ssh文件夹下新建config文件
Host relay.xxx.xyz
ControlPath ~/.ssh/master-%r@%h:%p
ControlMaster auto
ControlPersist yes

# iTerm Preferences → Profiles → General → Working Directory 选择Reuse Previous sessions's directory
```

