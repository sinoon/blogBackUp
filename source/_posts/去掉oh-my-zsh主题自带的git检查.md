title: 去掉oh_my_zsh主题自带的git检查
date: 2015-09-17 13:46:55
tags: oh_my_zsh git 主题
---
`oh_my_zsh`是一个非常好用和好看的命令行配置，它包含`自动补全`、`目录下git自动检查`、`主题`、`插件`等功能，给在命令行下工作的人提供了非常好的便利。

前阵子配置`vagrant`虚拟机，为了打个环境给同事使用。考虑到有些命令需要在虚拟机里面完成，所以还是安装了一个`oh_my_zsh`。

但是问题来了，在`oh_my_zsh`的默认主题中，当进入一个是`git`仓库的文件夹时，会自动读取`.git`里面的内容，了解当前的仓库状态，比如当前分支。可是在虚拟机里读取文件的速度要慢一些，这就会导致每一条命令都会检查一下当前仓库状态的这个行为会使得命令行开始输入的状态变的很慢。

一开始我以为是`插件`的原因，去掉`git`插件，`sourse ~/.zshrc`重载配置之后，还是如此，后来发现是`主题`的缘故。

我通常是比较懒的，既然默认主题有这个问题，那就找一个别的主题好了。

可是在`oh_my_zsh`的`github`上的`theme`列表中，每个主题都是含有这个功能的，所以只有自己修改主题了。

```bash
// 进入用户主目录
cd ~

// 进入oh_my_zsh的主题目录，这里保存着所有可以在其github上的主题库，我们只要改名字就可以引入对应的主题
cd .oh-my-zsh/themes

// 编辑默认主题文件
vim robbyrussell.zsh-theme
```
打开默认主题文件会发现其实就几行，像下面这样
```bash
local ret_status="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ %s)"
PROMPT='${ret_status}%{$fg_bold[green]%}%p %{$fg[cyan]%}%c %{$fg_bold[blue]%}$(git_prompt_info)%{$fg_bold[blue]%} % %{$reset_color%}'

ZSH_THEME_GIT_PROMPT_PREFIX="git:(%{$fg[red]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%}"
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[blue]%}) %{$fg[yellow]%}✗%{$reset_color%}"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[blue]%})"
```

需要将其修改为
```bash
local ret_status="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ %s)"
PROMPT='${ret_status}%{$fg_bold[green]%}%p %{$fg[cyan]%}%c %{$fg_bold[blue]%} % %{$reset_color%}'

# ZSH_THEME_GIT_PROMPT_PREFIX="git:(%{$fg[red]%}"
# ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%}"
# ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[blue]%}) %{$fg[yellow]%}✗%{$reset_color%}"
# ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[blue]%})"
```
也就是去掉git部分就好了，其他功能照常使用即可。
