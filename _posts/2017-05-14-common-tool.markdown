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


### JetBrains激活
-	http://xidea.online
-	http://idea.iteblog.com/key.php


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


### VSCode （windows）
- 删除整行	ctrl+shift+k
- 变量重命名	ctrl+F2


### VI
#### 插入
-	i，I：插入，小写插入到光标位置，大写插入到一行开始 
-	a，A：追加： 小写在光标位置追加，大写行尾追加 
-	o，O：插入空白行，小写在当前行下方，大写在当前行上方

#### 光标移动
-	h，j，k，l：上下左右移动
-	w，W： 向前跳过一个单词，停留在单词头部，小写考虑拼写，大写不考虑
-	e，E：向前跳过一个单词，停留在单词尾部
-	b，B：回退跳过一个单词
-	^ ：一行开始 `shift + 4`
-	$：一行结束 `shift + 4`
-	5G：一次跳过5行
-	ctrl+f ctrl+b: 往前翻、往后翻
-	N + %: 跳转到文件的N%处

#### 剪切复制（Cut and Paste）
-	yy：（yank、copy） 复制一行
-	2yy ：复制2行
-	yw：复制一个单词
-	y$：复制到行尾部
-	p，P：（put、paste）粘贴
-	dd：删除一行
-	dw：删除光标所在单词
-	x：删除一个字符

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

-  conda安装找不到安装
``` python
anaconda search -t conda mleap
anaconda show Anaconda-Extras/mleap
conda install --channel https://conda.anaconda.org/Anaconda-Extras mleap
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

### Excel 常用技巧
-	快速选取资料 command + shift + 上下左右方向
-	转置  复制右击选择性粘贴，转置
-	条件定位  F5 或者 ctrl + G
-	批量填充选中单元格  输入第一个，然后 ctrl+enter 
-	批量填充空格  F5条件定位选择空值，输入要填充的内容，ctrl+enter 填充全部
-	替换 ctrl + H

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
