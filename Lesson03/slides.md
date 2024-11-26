---
theme: seriph
title: 聊聊那些命令行的编辑器...
class: text-center
transition: slide-left
mdc: true
overviewSnapshots: true
---

# 命令行的编辑器

软件研发部 运维组

---
layout: quote
---

# 古老的编辑器们

在这个蔚蓝色的星球上，流传着两大神器的传说：据说 Emacs 是神的编辑器，而 Vim 是编辑器之神。

追求独步天下的高手和低手们争着一睹它们的风采，可看到它们朴素单薄的界面后，不禁心下怀疑：这就是神器吗？甚至有人生了轻视之心。

肤浅的人嗤之以鼻，说：什么年代了，还抱着这么老土的玩意不放，真他妈Geek！同学，请冷静下来，听我说：它们的确够老了，都几十年的寿命了，但你想想为什么，为什么这么古老的编辑器，却有越来越多的人皈依它们。

一 些人勇敢地拾起了 Vim 或 Emacs，却发现学习曲线陡峭而漫长，于是在没发现它们的强大之前就放弃了，说：太难用了，把键盘当鼠标用的烂玩意，有什么好的？

---
layout: quote
---

# 编辑器学习曲线图

![曲线图](./public/learing_curves_of_editors.png)

---
layout: quote
---

# 为什么他们能长久不衰？


曾几何时，Windows 用户对软件的可扩展性没有概念，他们只能对他们使用的软件进行非常有限的定制。扩展软件的权利保留在软件开发者手中。软件的使用者 如果想要新的功能和特性，只能等待软件的升级。有能力的用户等不及了，为了添加自己想要的功能，从0开始写了一款新的软件。就这样，新的功能意味着新的软件，Windows 下的软件前赴后继，迅速地更新换代着。因此，Windows 下的软件都很短命。

Linux 和开源软件渐渐流行起来，人们才发现：可扩展性才能给软件强大的生命。 在MS的VS横行的今天，Eclipse 为什么被评为最好的IDE？就是因为它在IDE中最具可扩展性。 在IE几乎一统天下的时候，为什么Firefox能夺走越来越多的用户，也是因为它的可扩展性。提供了良好的扩展接口，用户自然会写出各种各样的插件，来满足用户自己形形色色的要求。这样，软件在用户的推动下自然变得强大了。

---
layout: quote
---

#  为什么学习Vim？

Vi/Vim 操作的核心在于它的模式化设计，以及它极为高效的键盘快捷键。这种方式强调在不离开键盘的情况下，快速执行各种操作，而不是频繁切换到鼠标或其他输入方式。
现在的服务器上，基本都会预装Vi编辑器。我们需要ssh进服务器临时修改一些配置文件的时候，Vi是一个不错的选择。

# Vim的三个模式

- 普通模式: 通过motion和operator进行各种操作
- 插入模式: 编辑和添加文字
- 可视模式: 选择一些内容(除了可视行和可视块，其他情况不推荐使用，你会找到更加高效的实现办法的！)

---
layout: quote
---
 
# 开始我们的Vim旅程！

最佳入门教程: `vimtutor`

这里我们简单讲讲前四章的内容，后续各位可以自行阅读～

进阶补充: 
  - (Just Vim it)[https://vim.nauxscript.com/]
  - (指尖飞舞：vscode + vim 高效开发)[https://www.bilibili.com/video/BV1z541177Jy]

---
layout: quote
---

# Vim配置

> 免责声明：这里仅代表个人配置，可能不适用于所有人，根据自己爱好调整即可。

```shell
set ignorecase
set smartcase
set incsearch
set hlsearch
set expandtab
set tabstop=8
set shiftwidth=4
set autoindent
set cindent

inoremap jj <ESC>
```
