---
date: 2024-04-05
categories:
  - Mac
search:
  boost: 2
hide:
  - footer
---

## 终端美化

### [iTerm2](https://iterm2.com/)

### [Homebrew](https://brew.sh/)

MacOS 包管理工具

```bash

# 单独指定 brew 和 core 的repo地址
export HOMEBREW_BREW_GIT_REMOTE="..."  # put your Git mirror of Homebrew/brew here
export HOMEBREW_CORE_GIT_REMOTE="..."  # put your Git mirror of Homebrew/homebrew-core here

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### [Oh-My-Zsh](https://ohmyz.sh/)

```bash
# 安装 Oh-My-Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### [powerlevel10k](https://github.com/romkatv/powerlevel10k)

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

# 无法科学上网
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

### [starship](https://github.com/starship/starship)

```bash
curl -sS https://starship.rs/install.sh | sh

# HomeBrew按照
brew install starship
```

## neofetch

```bash
# 安装
brew install neofetch

# 使用
neofetch
```

## Tmux

```bash
brew install tmux
```

- [tmux cheatsheet](https://tmuxcheatsheet.com/)
- [Dark theme for tmux](https://draculatheme.com/tmux)
- [tmux 配置文件分享](https://www.amjun.com/2382.html)
- [github.comgpakosz/.tmux ](https://github.com/gpakosz/.tmux/blob/master/.tmux.conf)

## ARM 电脑安装加速 Pytorch

[Accelerated PyTorch training on Mac](https://developer.apple.com/metal/pytorch/)

## 宁芝 Niz Plum 蓝牙连接

### 第一次连接设备

- 不要让键盘和电脑使用 USB 连接
- 长按键盘电源键开启键盘
- 打开电脑蓝牙连接
- 按住键盘的 `Fn + Del` 至电源按钮左下方灯光持续闪烁，进入配对状态
- 电脑界面提示输入 `6` 位数字并按 `Enter` 键确认即可连接
