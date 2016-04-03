title: 一周成为Emacs新手
date: 2016-03-23 20:51:29
categories: ONE
tags:
    - one
    - Emacs
---

### 前言
#### why Emacs
* 逼格高。当你编程时小伙伴问你用的什么编辑器时，你默默的说出一个古董般的名字，这感觉还是很酸爽的。
* 师兄用。统一工具所带来的好处是，当你遇到问题后，可以直接将键盘交给师兄，然后看他在你的电脑上用200apm的手速帮你处理问题，并给你悉心指导。
* Eshell。自从有了Eshell，连上服务器的第一件事就是打开Emacs。
* Elisp。什么？貌似还能多学一门很奇怪的语言。
* 虽然我很喜欢`hjkl`，但我不喜欢按`ESC`。
* 听说这是一个伪装成编辑器的操作系统。

#### why THIS
一开始接触Emacs是手足无措的。虽然有[一年成为Emacs高手](<https://github.com/redguardtoo/mastering-emacs-in-one-year-guide/blob/master/guide-zh.org>)这样的牛文，可以给出很多很棒的建议。但是，在我们所需要面对实际场景中还是有些别扭：我很赞同直接去clone别人配置的方法，但是很多东西clone下来以后，研究、理解、熟悉快捷键都需要时间，到最后有可能我们并不需要这样的配置；更多情况是，高手和大师的配置大都缺少说明，上手时间长，每个功能靠什么插件实现都不够明晰。 
因此，我对于学习Emacs产生了新的要求：
* `快速上手`——有清晰的配置说明文档，针对操作、插件
* `功能清晰`——适应服务器cpp编程环境（补全，缩进，跳转，查找），能够兼容python的缩进（补全暂未着手实现）

针对我这一个月的Emacs学习和使用情况，根据现有服务器线上环境，我自己配置了一个满足我的需求的Emacs编辑配置，见<git@gitlab.alibaba-inc.com:zhixiao.yw/emacs.d.git>，其主要理念是：
* 用`尽可能少`的插件实现线上基本编程工具的基本功能
* 能有清晰明了的说明文档(`本文`)，可以解释针对插件配置时为什么这么选和如何快速上手使用的问题。

其中使用的插件，它们的基本功能以及我选择它们的原因都会在本文中进行阐述。希望能够指导大家达到忽略环境变化快速上手编程的目的，一周入门成为Emacs新手，剩下的时间，可以根据自己造化再慢慢走向高手之路。

<!-- more -->

### 基本概念
#### 版本及安装
* 强烈`建议`使用24.5版本的Emacs
 * 更好的插件支持，比如helm和company
 * 集成`package`管理器
 * 更高版本且`自集成`的cedet
 * 本文及其配置都是在这个版本下进行的
* 在Mac和Win下安装，直接去找可执行安装文件即可，这里就不赘述，主要讲一下服务器上安装。一般服务器上都已经安装好了Emacs，不过版本较低，大概在23.2左右。
 1. 在<http://ftp.gnu.org/gnu/emacs/>下载需要的源码包：`wget http://ftp.gnu.org/gnu/emacs/emacs-24.5.tar.gz`
 2. 解压：`tar -xzvf emacs-24.5.tar.gz`
 3. `./configure`。缺啥包安装啥包就行，都安装了还是有缺少，直接--without掉。我遇到的是`libXpm,libgif,libtiff`三个包没有，直接根据提示信息without掉是没问题的。
 4. `make`
 5. `make install`，如果报文件创建失败的permission，就上管理员账户安装。这一步过后，会将emacs-24.5安装在在`/usr/local/bin/`中，试着在命令行输入`emacs-24.5`应该就可以打开我们新版本的Emacs了。
* 打开Emacs后的第一件事，强烈建议使用`C-h t`命令打开Emacs Tutorial，花半个小时通读一遍，这样，对于Emacs的许多疑惑都会得到解答，上手更快。
 
#### buffer和window
* buffer是指一个内容缓存区。
 * 其并不代表一个文件。
 * 任何显示的内容都是以buffer的形式存在的，无论是Eshell还是普通文件。
* window是用来显示buffer的一块区域。
 * 关闭一个window并不等于关闭了一个buffer。
 
#### 模式
在Emacs中，并没有像Vim那样明确的模式概念，任何情况下，我们都处于编辑的状态，但是区别在于和需要注意的是，当前在哪个buffer中进行编辑及操作。
针对不同的buffer有不同的快捷操作方法，比如在`buffer list`用`d`和`x`快速删除多个buffer。再比如在helm中用`C-p`和`C-n`进行上下项的选择。

#### 配置
目前选用了如下几个插件，针对Emacs的改动也仅限于这些，好处是看看快捷键很快就能上手了，缺点就是功能还不够全。其详细功能讲解及配置内容会在本文[第四部分](#4)进行说明和讲解。
* helm：一个非常强大且全面的插件。首先是一个minibuffer扩展，可以完全用helm替换默认的`M-x`和`C-x f`，支持模糊查找，显示的功能更全，定位更快。本文中只使用了Helm非常少的功能。(`目前我已改回用smex+ido，更快更简洁`)
* company：自动补全工具，良好的支持Emacs集成的semantic词法分析工具。
* window-numbering：快速切换窗口的工具。
* google-c-style：谷歌的代码规范。
* cedet：Emacs自集成的开发环境工具集。

### 基本操作
首先，我们需要了解Emacs中快捷键的起点键和快捷键的描述方法，如我们关闭Emacs的快捷键描述为`C-x C-c`，操作为两步：CTRL+x后CTRL+c，再比如，保存buffer快捷键`C-x s`就是CTRL+x后单独按s，再比如，我们打开Emacs的命令行使用命令`M-x`，其中`M`代表meta键，在不同的系统上这个键可能会不同，一般为`ALT`，不过我一开始在Mac上使用的时候，这个键为`ESC`，不过这个可以在系统里更改，再比如CTRL不好按，就可以把它和caps键呼唤，习惯以后就可以入手价值不菲的编程大杀器`HHKB--HAPPY HACKING KEYBOARD`。配上Mac再来套彩虹色键帽，啧啧。

#### 关闭Emacs
请按`C-x c`。

#### 光标移动
|keys     |description|
|---------|-----------|
|C-p,C-n,C-b,C-f|光标上下左右|
|C-a,C-e,M-a,M-e|行首行尾，句首句尾|
|M->,M-<<|文件首文件尾|

#### 文本编辑——选择复制粘贴剪切删除
|keys     |description|
|---------|-----------|
|C-SPC|标记|
|C-w,M-w|剪切，复制标记内容|
|C-y,M-y|粘贴，粘贴历史内容（第一个操作后如果不是自己想要的可以通过第二个操作循环上选）|
|C-b,M-d|删除后一个字符，删除后一个词|
|C-k|删除到行尾|
* Tips：如何快速复制一行？
 * `C-a,C-k,C-y`

#### 查找
|keys     |description|
|---------|-----------|
|C-s|向后查找，重复命令光标会向后持续搜索|
|C-r|向前查找，重复命令光标会向前持续搜索|
|M-x,grep-find|在目录内查找所有文件中的指定内容|
* Tips：如何快速查找光标所在的Tag？
 * 将光标移动到单词首，`C-s`（C-r）打开搜索，`C-w`选中单词（重复命令会持续向后选中），`C-s`进行搜索

#### 窗口分割
|keys     |description|
|---------|-----------|
|C-x 1|关闭其他窗口|
|C-x 2|垂直分割窗口|
|C-x 3|水平分割窗口|
|C-x 0|关闭当前窗口|
|C-x o|转到下一个窗口|

#### buffer与file
|keys     |description|
|---------|-----------|
|C-x b|打开buffer列表|
|C-x C-f，C-x 4 f|打开一个文件，在新窗口打开一个文件|
|C-x k|关闭当前buffer|
|C-x s|存储当前buffer|

### 配置一个可编程工具——补全，跳转，引用
#### 从CEDET说起
[CEDET](http://cedet.sourceforge.net/symref.shtml)是Collection of Emacs Development Environment Tools的缩写，目前最新的独立版本为1.1，并且该版本是最后一个独立发布的版本，更高版本都已经集成在了较新版Emacs之中，**查看自己CEDET版本**，可以在`M-x`后输入`cedet-version`即可，如下图。

![image](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/zhixiao.yw/rookie_manual/3924189273/image.png)

可以看到，Emacs集成的CEDET拥有这样一些工具：
* Semantic，词法分析器，本文主要依靠该工具进行代码分析，作为自动补全工具后端，同时也使用了该工具的快速跳转和引用查询功能
* EDE，项目管理工具，通过设置项目的根目录，头文件目录和系统头文件目录，为Semantic提供支持。
 * Tips:如何获得系统的c++头文件目录:`echo "" | g++ -v -x c++ -E -`
* EIEIO，没弄懂，:P。
* SRecode，代码模板管理器，暂未使用。
* eassist，提供.h/.cpp切换和taglist功能。

具体配置文件见lisp/init-cedet.el，其主要功能为：
* 代码分析，生成db文件
* 定义跳转
* 引用查找
* 在minibuffer里显示光标所在函数的summary。

主要功能绑定快捷键如下：

|keys|description|
|---|---|
|C-c j|根据光标tag跳转到定义|
|C-c,C-r|查找所有引用|
|C-c t|在.h和.cpp文件中跳转|
|C-c e|列出当前文件的method list，可以进行选择快速定位|

因为后面补全采用了company-mode，所以在快捷键设置上，我取消了`M-TAB`和`C-M-i`这两个原来绑定在Semantic补全上快捷键。

#### company-mode
作为一个选择综合症患者，一开始在这个和ac-complete，auto-complete之间纠结了很久。网上也有许多关于他们之间优劣的辩论，我主要因为这个东西配置方便，跟Semantic结合不错，速度也还好而选择了它。它可以支持Elisp, Clang, Semantic, Eclim, Ropemacs, Ispell, CMake, BBDB, Yasnippet, dabbrev, etags, gtags, 文件, 关键字等多种backends，并且会自动选用当前可以使用的backends，非常方便效果如下：

![image](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/zhixiao.yw/rookie_manual/26953b1346/image.png)

配置代码也相当简短，见lisp/init-company.el。根据注释，大致可以知道每一个语句的意义。其中require-package是定义的一个函数，主要用途是根据package-name安装一个package，详见lisp/init-elpa.el文件。

```
;;;; init-company.el
;;company-mode
;; install company-mode
(require-package 'company)
;; include company
(require 'company)
;; 将显示延时关掉
(setq company-idle-delay 0)
;; 开启补全
(setq company-mode t)
;; 添加全局补全
(add-hook 'after-init-hook 'global-company-mode)
;; 定义一个本文件的外部调用接口
(provide 'init-company)
```

快捷键定义，在company候选列表中:

|keys|description|
|---|---|
|M-n,M-p|company列表里选项上下移动|
|M-(digit)|快速选中列表中的项|
|RET|补全|
|TAB|补全公共部分|
|M-TAB|绑定到了company-complete-common上，可以手动调出company补全列表|

#### Helm
Helm是一个minibuffer的增强工具，可以替代原有的`M-x`，`C-x C-f`，`C-x b`的功能，还具备如下几个优良特性：
* 模糊查找，可以通过键入内容来筛选当前列表中的内容
* 支持历史记录，可以快速选择以前使用过的文件或者命令
* 可以显示命令所绑定的快捷键，这个对于新手记快捷键还是蛮好的，有些命令偶尔看到了记上一眼，两三次就记住了
* 显示更加直观，更cool

效果如下，已`M-x`为例：

![image](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/zhixiao.yw/rookie_manual/b46ba97963/image.png)
           
基本操作如下：

|keys|description|
|---|---|
|C-n,C-p|Helm列表里选项上下移动|
|C-l,C-j|回到上一层目录，进入所选目录（C-x C-f时）|
|C-o|选择另外一个列表，用在历史内容和所有内容切换的时候|
|RET|打开文件，使用命令|
|C-SPC|标记选项|

helm是一个十分强大的工具，在不同的状态下其快捷键也不同，十分丰富。每次显示出Helm，其都会显示小部分操作命令在状态栏，同时也会指示用户，可以通过`C-m h`来获得更多信息。

#### window-numbering
通过快捷键迅速选定窗口。其编号主要是从由左向右由上向下的顺序。快捷键也只有一组`M-(digit)`。

#### *其他特性*
* 用y和n来代替yes和no
* 取消了烦人的自动备份
* 自定义了`grep-find`命令，在Semantic不管用的时候，用这个吧。
* Tab为什么没有用了？
 * tab被缩进给占用了，可以试试`M-i`

### 5 插件管理
我采用的Emacs配置结构为：

```
.
├── elpa
│   ├── archives
│   ├── async-20150724.2211
│   ├── company-20150727.1415
│   ├── ggtags-0.8.10
│   ├── google-c-style-20140929.1118
│   ├── helm-20150805.54
│   ├── helm-core-20150805.206
│   └── smex-3.0
├── config
├── eshell
│   ├── history
│   └── lastdir
├── init.el
├── lisp
│   ├── eassist.el
│   ├── hober-theme.el
│   ├── init-cedet.el
│   ├── init-company.el
│   ├── init-elpa.el
│   ├── init-ggtags.el
│   ├── init-google-c-style.el
│   ├── init-helm.el
│   ├── init-smex.el
│   ├── init-utils.el
│   └── window-numbering.el
├── recentf
└── semanticdb
```

其中：
* init.el为Emacs启动时加载的文件
* config用来存储ede的project配置
* elpa为package管理器安装管理的包文件夹
* lisp为手动下载或设置的插件配置文件
 * 以init开头的为各个模块的初始化文件，其中过程主要为先通过package下载安装，然后为配置内容，最后提供一个接口供init.el调用
* 剩下的是Emacs或其插件自动生成的文件，可以不用在意

我在`init-elpa.el`配置了包管理器的下载源，并定义了两个通过package-name进行包安装的接口。在init.el文件中，直接将lisp文件夹添加进Emacs的`load-path`里即可。
在第一次启动Emacs时，其先会打开配置文件init.el，然后通过init.el调用lisp里的各个文件，插件包也会自动下载并安装到elpa文件夹中。

### 推荐
* [加速Emacs启动方法的技巧](http://emacser.com/daemon.htm)
* [一年成为Emacs高手](https://github.com/redguardtoo/mastering-emacs-in-one-year-guide/blob/master/guide-zh.org)
* [oh-my-emacs配置](https://github.com/xiaohanyu/oh-my-emacs)
* [MELPA插件源](http://melpa.org/#/)，可以看看有哪些流行插件

