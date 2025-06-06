---
categories:
  - Mac
---
## 安装 Xcode

在应用商店中安装更新 **Xode**，或者在安装 **HomeBrew** 时，内部自动安装、更新 **Xode Command Line Tool**。

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

- [iTerm2](https://iterm2.com/)
- [WezTerm](https://wezfurlong.org/wezterm/index.html)
- [Warp](https://www.warp.dev/)
- [Alacritty](https://alacritty.org/)

## zsh 优化

- zsh: [https://www.zsh.org/](https://www.zsh.org/)
- [Oh-My-Zsh](https://ohmyz.sh/)

```bash
# 安装 Oh-My-Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**插件**

[Zsh community projects.](https://github.com/zsh-users)

- zsh-syntax-highlighting
- zsh-autosuggestions
- zsh-completions

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
```

安装完成后，修改 `~/.zshrc` 文件。

```bash title="~/.zshrc"
plugins=( 
    # other plugins...
    zsh-autosuggestions
    zsh-syntax-highlighting
    zsh-completions
)
```

**主题**

[starship](https://github.com/starship/starship) 和 [powerlevel10k](https://github.com/romkatv/powerlevel10k) 二选一即可。


```bash title="starship"
# 安装最新版本
curl -sS https://starship.rs/install.sh | sh

# 初始化, 需要将此命令追加到 ~/.zshrc 文件中
eval "$(starship init zsh)"
```

```bash title="powerlevel10k"
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

# 无法科学上网
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

## Vim

```bash
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

> [The Ultimate vimrc](https://github.com/amix/vimrc)

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

- OrbStack: [https://orbstack.dev/](https://orbstack.dev/)

更加轻量、高效的 MacOS Docker工具。


## VS Code

- 勾选符合自己版本下载。
- 下载：[https://code.visualstudio.com/#alt-downloads](https://code.visualstudio.com/#alt-downloads)


应用下载后添加配置

```json title="setting.json"
{
  "terminal.integrated.defaultProfile.osx": "zsh",
}
```

**VS Code Cli**

`command + shift + p` 之后搜索 code，选择 `Shell Command: Install 'code' command in PATH`。