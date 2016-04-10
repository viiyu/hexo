title: 使用TMUX
date: 2016-04-10 20:21:23
tags:
    - tmux
    - one
    - tool
---

## WHY
* 经常遭遇断线的窘境，tmux可以保存session内容，让断线重连后依然恢复到断线前的编辑状态
* 单独使用iTerm之类的terminal增强工具做多窗口管理，还是略显不够
* 不太适应screen这个工具，略显陈旧

## 安装
* yum、apt-get等等都可以方便安装
* 源码安装的话需要注意先安装libevent

<!-- more -->

## 使用
### 配置
将相应的配置写入用户目录下`.tmux.conf`文件中，下面是我的一个基本配置，主要是把激活快捷键映射到更舒适的位置，因为我需要配合emacs使用，`C-b`经常要用到，于是重新绑定到`C-q`。然后将操作模式设置为emacs模式，这样在tmux选择窗口之类的场景下，可以使用emacs的快捷键进行操作。
```
set-option -g prefix C-q
unbind-key C-q
bind-key C-q send-prefix

setw -g mode-keys emacs
```

### 快捷键

具体的功能可以看看man手册或者需要的时候上网查查，因此在这里只稍微提一下我比较常用的并能帮助大家快速上手的快捷键部分。

#### 启动tmux
```
tmux            //开启一个新session
tmux ls         //列出当前存在的session，
tmux attach     //重新连接到上一次退出的session中
tmux attach -t session_id     //连接到指定的session_id中
```

#### tmux常用快捷键
在tmux中，需要先使用快捷键激活控制台，然后才能使用tmux的功能快捷键。默认为`C-b`。

|按键|功能|备注|
|---|---|---|
|?|打开帮助文档||
|c|创建一个新的窗口|已打开窗口会显示在左下方，并用*标记当前窗口|
|n|切换到下一个窗口||
|p|切换到上一个窗口||
|w|选择窗口|若像我一样使用emacs的mode-key，则可使用C-n或C-p进行选择|
|数字键|选择窗口||
|x|关闭当前窗口||
|d|断开session|相当于退出tmux，可以使用tmux attach重新连接回来|
|[|进入copy-mode|在这个模式可以翻页，复制，快捷键与设定的mode-keys的模式相同|
|]|粘贴copy-mode中所选内容|


