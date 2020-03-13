---
title: linux基础
date: 2020-02-10 17:05:25
tags:
---

## linux文件系统

#### •Filesystem Hierarchy Standard（文件系统层次化标准）

`–/boot: 系统启动相关的文件，如内核、initrd，以及grub(bootloader)`

`–/dev: 设备文件` 

`–/etc：配置文件`

`–/home：用户的家目录，每一个用户的家目录通常默认为/home/USERNAME`

`–/root：管理员的家目录；`

`–/lib：库文件`

`–/media：挂载点目录，移动设备`

`–/mnt：挂载点目录，额外的临时文件系统`

`–/opt：可选目录，第三方程序的安装目录`

`–/proc：伪文件系统，内核映射文件`

`–/sys：伪文件系统，跟硬件设备相关的属性映射文件`

`–/tmp：临时文件, /var/tmp`

`–/var：可变化的文件`

`–/bin: 可执行文件, 用户命令`

`–/sbin：管理命令`

<!-- more -->

### linux常用命令

`•df：显示磁盘使用情况`

`•du：显示文件系统使用情况`

`•ls：显示目录`

`•cd：切换工作目录`

`•pwd：显示当前工作目录`

`•mkdir：创建目录`

`•rm：删除`

`•cp：拷贝`

`•mv：移动`

`•ln：链接`

`•stat：元数据`

•df：显示磁盘使用情况

–df -h

•du：显示文件系统使用情况

–du -sh

–du -a

•ls：显示目录

–ls -alhrt

•cd：切换工作目录

–cd 或 cd ~

–cd ..

–cd /

–cd -

•pwd：显示当前工作目录

•mkdir：创建目录

–mkdir -p ./a/b/c

–mkdir a/{1,2,3}dir

•rm：删除

–rm -f

–rm -rf /

•cp：拷贝

–cp /etc/{profile,inittab} ./

–cp -r ./a ./new

–cp -l cp -s

•mv：移动

•ln：链接

–ln 默认硬链接

–ln -s 软链接

### 文本操作命令

•cat

–more

–less

–head

•head -2 /etc/profile

–tail

•tail -2 /etc/profile

•tail -f

•管道

•cat b.txt | head -3

• | tail -1

### yum 包管理

1. 使用 yum 查找包
 `yum search`
2. 列出可安装的软件包
 `yum list `
3. 列出所有可更新的软件包
`yum list updates`
4. 列出所有已安装的软件包
`yum list installed`
5. 列出所有已安装但是不在yum repository内的安装包
`yum list extras`
6. 列出指定的软件包
`yum list`
7. 列出所有软件包的信息
`yum info`
8. 列出所有可更新的软件包信息
`yum info updates`
9. 列出软件包提供哪些文件
`yum provides`


### vi命令

•打开文件

–vim /path/to/somefile

–vim +# :打开文件，并定位于第#行 

–vim +：打开文件，定位至最后一行

–vim +/PATTERN : 打开文件，定位至第一次被PATTERN匹配到的行的行首

•关闭文件

–末行模式：

–:q 退出 没有动过文件

–:wq 保存并退出  动过了，不后悔

–:q! 不保存并退出 动过了，后悔了

–:w 保存

–:w! 强行保存

–:wq --> :x

–

–ZZ: 保存并退出  不需要冒号，编辑模式

•编辑-->输入：

– i: 在当前光标所在字符的前面，转为输入模式；

– a: 在当前光标所在字符的后面，转为输入模式；

–

– o: 在当前光标所在行的下方，新建一行，并转为输入模式；

– O：在当前光标所在行的上方，新建一行，并转为输入模式； 

– I：在当前光标所在行的行首，转换为输入模式

– A：在当前光标所在行的行尾，转换为输入模式

– 输入-->编辑：

•ESC

•编辑-->末行：

–：

•末行-->编辑：

–ESC, ESC

•移动光标

–字符

•h: 左；j: 下；k: 上；l: 右

–单词

•w: 移至下一个单词的词首

•e: 跳至当前或下一个单词的词尾

•b: 跳至当前或前一个单词的词首

–行内

•0: 绝对行首

•^: 行首的第一个非空白字符

•$: 绝对行尾

–行间

•G:文章末尾

•3G:第3行

•gg:文章开头

–翻屏

•ctrl：f，b

•删除&替换单个字符

–x:删除光标位置字符

–3x:删除光标开始3个字符

–r:替换光标位置字符

•删除命令 ： d 

–dw，dd

•复制粘贴&剪切 

–yw，yy

–p

–P

•撤销&重做

–u  撤销

–ctrl+r 重做 撤销的操作

–. 重复上一步的操作

### 文本处理

•cut：显示切割的行数据

–f：选择显示的列

–s：不显示没有分隔符的行

–d：自定义分隔符

•sort：排序文件的行

–n：按数值排序

–r：倒序

–t：自定义分隔符

–k：选择排序列

–u：合并相同行

–f：忽略大小写

