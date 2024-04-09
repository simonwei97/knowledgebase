---
date: 2024-04-05
categories:
  - Mac
search:
  boost: 2
hide:
  - footer
---

```bash
# 安装tmp插件
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

编辑 tmux 本地配置 `~/.tmux.conf`。

```conf
# Set vi key bindings mode
set -g mode-keys vi
set -g status-keys vi

# Change prefix from 'Ctrl+B' to 'Ctrl+A'
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# start windows numbering at 1
set -g base-index 1
# make pane numbering consistent with windows
setw -g pane-base-index 1
# rename window to reflect current program
setw -g automatic-rename on
# renumber windows when a window is closed
set -g renumber-windows on

# boost history
set -g history-limit 20000

# reload config
bind r run '"tmux" source $HOME/.tmux.conf' \; display "$HOME/.tmux.conf sourced"

# pane navigation
bind -r h select-pane -L  # move left
bind -r j select-pane -D  # move down
bind -r k select-pane -U  # move up
bind -r l select-pane -R  # move right
bind > swap-pane -D       # swap current pane with the next one
bind < swap-pane -U       # swap current pane with the previous one

# Set new panes to open in current directory
bind c new-window -c "#{pane_current_path}"
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"

# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'tmux-plugins/tmux-sidebar'
set -g @plugin 'dracula/tmux'

# Config Dracula Theme
set -g @dracula-show-fahrenheit false
set -g @dracula-show-network false
set -g @dracula-cpu-usage true
set -g @dracula-ram-usage true
set -g @dracula-day-month true
set -g @dracula-military-time true
set -g @dracula-show-flags true

# available plugins: battery, cpu-usage, git, gpu-usage, ram-usage, tmux-ram-usage, network, network-bandwidth, network-ping, ssh-session, attached-clients, network-vpn, weather, time, mpc, spotify-tui, kubernetes-context, synchronize-panes
set -g @dracula-plugins "git time cpu-usage ram-usage kubernetes-context"

set -g @dracula-show-powerline true
# it can accept `hostname` (full hostname), `session`, `shortname` (short name), `smiley`, `window`, or any character.
set -g @dracula-show-left-icon shortname

# available colors: white, gray, dark_gray, light_purple, dark_purple, cyan, green, orange, red, pink, yellow
# set -g @dracula-[plugin-name]-colors "[background] [foreground]"
set -g @dracula-kubernetes-context-colors "light_purple dark_gray"
set -g @dracula-git-colors "white dark_gray"
set -g @dracula-time-colors "green dark_gray"
set -g @dracula-cpu-usage-colors "pink dark_gray"
set -g @dracula-ram-usage-colors "cyan dark_gray"

set -g @dracula-fixed-location "ShenZhen, China"
set -g @dracula-time-format "%A %F %R"

# Set 256 colors
set -s default-terminal 'tmux-256color'

# copy from tmux to clipboard
bind-key -T copy-mode-vi y send-keys -X copy-pipe "xclip -r" \; display-message "Copied to selection"
bind-key -T copy-mode-vi Y send-keys -X copy-pipe "xclip -r -selection clipboard" \; display-message "Copied to clipboard"
bind-key C-p run-shell "xclip -o | tmux load-buffer - && tmux paste-buffer"

# turn mouse on
set -g mouse on
# restore vim sessions
set -g @resurrect-strategy-vim 'session'
# restore neovim sessions
set -g @resurrect-strategy-nvim 'session'
# restore panes
set -g @resurrect-capture-pane-contents 'on'
# restore last saved environment (automatically)
set -g @continuum-restore 'on'
set -g @continuum-save-interval '15'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

[^1]: [Dark theme for tmux](https://draculatheme.com/tmux)
[^2]: [tmux 配置文件分享](https://www.amjun.com/2382.html)
[^3]: [github.comgpakosz/.tmux ](https://github.com/gpakosz/.tmux/blob/master/.tmux.conf)
