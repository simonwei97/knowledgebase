---
categories:
  - Mac
---
## 安装 Xcode

在应用商店中安装更新Xode，或者在安装 HomeBrew时，内部自动安装、更新 `Xode Command Line Tool`。

## [Homebrew](https://brew.sh/)

MacOS 包管理工具

```bash

# 单独指定 brew 和 core 的repo地址
export HOMEBREW_BREW_GIT_REMOTE="..."  # put your Git mirror of Homebrew/brew here
export HOMEBREW_CORE_GIT_REMOTE="..."  # put your Git mirror of Homebrew/homebrew-core here

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Git安装

```bash
brew install git
```

## 终端工具

- iTerm2: https://iterm2.com/
- WezTerm: https://wezfurlong.org/wezterm/index.html
- Warp: https://www.warp.dev/
- Alacritty: https://alacritty.org/

## 终端美化

### [Oh-My-Zsh](https://ohmyz.sh/)

```bash
# 安装 Oh-My-Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

starship 和 powerlevel10k 二选一即可。

### [starship](https://github.com/starship/starship)

```bash
# 安装最新版本
curl -sS https://starship.rs/install.sh | sh

# 初始化
eval "$(starship init zsh)"
```

### [powerlevel10k](https://github.com/romkatv/powerlevel10k)

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

# 无法科学上网
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

## neofetch

快速获取关键系统信息

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


## Docker相关

OrbStack: https://orbstack.dev/

更加轻量搞笑的 MacOS Docker工具。

## Conda

Anaconda或者 MiniConda，MiniConda更加轻量化。Anaconda包含了更多的科学计算的工具包，安装包大，安装过程也比较繁琐。

- miniconda: https://docs.anaconda.com/miniconda/


## VsCode

下载应用：https://code.visualstudio.com/#alt-downloads。勾选☑️符合自己平台的版本下载。

应用下载后添加配置

```json title="setting.json"
{
  "terminal.integrated.defaultProfile.osx": "zsh",
}
```

### VsCode CLI

`command + shift + p` 之后搜索 code，选择 `Shell Command: Install 'code' command in PATH`。