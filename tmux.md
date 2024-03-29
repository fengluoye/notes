## 先上一个基础配置

- `vim ~/.tmux.conf`

```bash
# 先把默认的那个 ctrl + b 的变态快捷键改掉, 改为 "`", 数字 1 左边的那个键
bind ` send-prefix
set -g prefix `
unbind C-b
# ` + r 重新加载配置
bind r source-file ~/.tmux.conf \; display "Reloaded!"
# 把 window 和 pane 的初始序号改为 1 
set-option -g base-index 1
setw -g pane-base-index 1
# 设置 pane 分割的快捷键
# 左右分割
bind \ split-window -h
# 上下分割
bind - split-window -v 
# 鼠标支持
set-option -g mouse on
set -g history-limit 30000
set -g default-terminal "screen-256color"
set -g default-shell "$SHELL"
# 鼠标滚轮进入复制模式
bind -n WheelUpPane if-shell -F -t = "#{mouse_any_flag}" "send-keys -M" "if -Ft= '#{pane_in_mode}' 'send-keys -M' 'copy-mode -e'"
# 兼容 macOS
bind-key -T copy-mode-vi v send -X begin-selection
bind-key -T copy-mode-vi y send-keys -X copy-pipe-and-cancel "pbcopy"
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "pbcopy"
bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel "pbcopy"
```

## 配置 alias

如下配置可以直接在终端运行 tan1 就能进入tmux, 随时可以 ` + d dettach, 下次在输入 tan1 又可以 attach

```bash
# 加入 ~/.zshrc
alias tan1="tmux a -d -t 1 2>/dev/null || { sleep 0.5 ; tmux new -s 1 }"
# 解释:
## 如果存在 session 1, 那么就 attach, 并且 dettach 掉其他窗口的 session
## 如果不存在 session 1, 肯定会 attach 失败, 那么把错误信息 redirect 到黑洞 (2>/dev/null), 2代表错误信息的输出
## 接下来等待 0.5s, 然后新建一个 session 1. 
```

## 远程登陆直接启动tmux

```bash
ssh -qt user@ip zsh -ci tan1
## 解释:
## ssh 到 远程服务器, 执行 zsh 下面的 alias tan1, 一步到位的运行 tmux. 
```

## tmux 常规操作

- ` + \ or - 创建左右分割 和 上下分割的 pane, 可以创建无限个
- ` + 方向键, 在不同的 pane 里面切换
- ` + c , 创建一个 window, 相当于linux里面的虚拟桌面.
- \` + 数字, 在不同的 window 切换,  \` + n or p, 切换到下一个和上一个 window
- ` + d dettach, 相当于退出 tmux, 下次直接运行 tan1 可以随时 attach, 任务都还在
- ` + r 重新加载配置
- tmux kill-server 自杀模式, 杀死所有 tmux 进程
- tmux ls 列出 session
- tmux lsw 列出 window
- tmux lsp 列出 window 里面的 pane 
