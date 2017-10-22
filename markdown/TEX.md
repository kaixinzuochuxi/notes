#TEX
##基本要素
###作者，书本设计者，排版者
###布局设计

##输入元素
###空格

多个空格视为一个
开头空格省略
换行符视为空格

空格组成空行
空行表示另起一段
多个空行等于一个空行

###特殊字符

\# $ % ^ _ { } ~ \

转义！
表示原义：加\，\需用\textbackslash--\\\\用来换行

###命令

3种情况

    \加上若干52种字母，以空格数字或其他非字母结束
    \加上1个非字符
    命令后加*

所有情况，后面的空格都会被忽略

    如果需要加空格：
        {}
        \space

\command[optional parameter]{parameter


###注释

单行注释--%，当前行后续都不会被处理
分块注释--不可被其他环境包含

    \begin{comment}
    \end{comment}

##文章格式

###文章类型

    \documentclass[option]{class}
    class
        article
        proc--proceedings based on article
        minimal
        report
        book
        slides

    option
        10pt，11pt,12pt
        a4paper,letterpaper
        fleqn--formulae left-aligned instead of centred
        leqno--方程编号靠左不靠右
        titlepage,notitlepage--new page start after title or not
        onecolumn,twocolumn
        twoside,oneside
        landscape
        openright,openany


###导言区

包

    \usepackage{...}

作者

    \author{}

标题

    \title{}---正文开始后\makefile
###正文

    \begin{document}
    \end{document}

page style

    \pagestyle{style}
        plain--最下方正中间打印页码
        headings--在页眉打印
        empty--不显示

    \thispagestyle{}--临时改变

##常见格式


##大项目：

    导入
        \include{filename}--不需要后缀，新起一页来插入内容
        \input{}--不新起一页
        \includeonly{filename1,filename2...}

\usepackage{syntonly}???
\syntaxonly


----
##排版

###文章结构

    段落
        注意段落控制
            是否需要空格
            和其他环境共同出现时
    句子
    子句

###换行，换页

    换行不新起一段
        \\ or \newline
    强制换行后不换页
        \\*
    新起一页
        \newpage
    





















##目的

    文档质量更高
    编写更容易


##基本处理原则
顺序
    顺序处理   
    优先理解为组合字符

多个空格视为1个

控制
    控制序列后空格忽略
    

##常规输入与实际打印

字符

    可直接输入
        直接输入

        多次重复组成
            `,','',"
            做引用\lq, 右引用\rq
                特殊情况
                    引用后接空格：转义\
                    引用后接字符：空格
                    引用中引用：grouping,{'}''或'\thinspace''
            hypen,连接单词              -   
            en-dash,数字范围            --
            em-dash,文字破折号          ---
            minus sign,减号            $-$(所有数学符号都有$$)
        
    不可直接输入
        显示空格字符
        小空格字符 \thinspace

    区分行：
        1，l

##控制
矛盾：字符少，表示多，转义字符


control sequence
    control word
        组成：转义字符+1个或多个大小写字母
    control symbol
        组成：转义字符+1个非字母

空格如果必须要表示，不可被忽略--转义空格   
多个空格视为1个   
非打印字符转义视为空格

控制序列的组成

    底层，基础，不直接用--primitive
    稍高级，可直接用
    更高级，控制整个文档