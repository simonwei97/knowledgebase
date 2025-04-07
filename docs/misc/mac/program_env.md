## 1. Conda 开发环境配置

**Anaconda**
: - 包含了更多的科学计算的工具包，安装包大，安装过程也比较繁琐。
  - [https://www.anaconda.com/download/success](https://www.anaconda.com/download/success)

**Miniconda**: 
:  - 更加轻量化
  - [https://docs.anaconda.com/miniconda/](https://docs.anaconda.com/miniconda/)


对于 M系列芯片的 Mac 电脑，运行如下命令即可。

```bash
mkdir -p ~/miniconda3
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh -o ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
```

安装好后，初始化对应的 Shell 环境。

```bash
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

### Conda常用操作

```bash
# 查看当前的 Conda 环境
conda env list

# 指定特定的 Python 版本, 创建指定名称的环境
conda create -n ${ENV_NAME} python=3.x

# 激活当前的 Conda 环境
conda activate

# 退出当前的 Conda 环境
conda deactivate

# 删除创建的环境
conda env remove -n ${ENV_NAME}
```

## 2. Go 开发环境配置

进入下载地址下载对应版本的 pkg 包。[所有发布版本下载地址](https://go.dev/dl/)。选择 `go1.x.x.darwin-arm64.pkg` 下载。

下载完成后点击安装，按照引导操作即可。

```bash
# 查看安装的版本
go version

# 查看当前Go Env
go env
```

### 配置环境变量

选择自己对应的终端的配置文件, `zsh` 或者 `bash`。
```bash
# Zsh
vim ~/.zshrc

# Bash
vim ~/.bashrc
```

文件内容修改如下

```yaml
# Go
export GOPATH=/（你自己的路径）
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOBIN
export GO111MODULE=on

# 选择配置
export GOPROXY=https://mirrors.aliyun.com/goproxy/ 
# Go END
```

编辑好后保存, `source ~/.zshrc` 重载配置。或者打开新的终端窗口。


## 3. Rust 开发环境配置

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

运行后选择 **方式 1** 安装。之后重启终端即可。

```bash
# 查看安装的 Rust 版本
rustc --version

cargo --version
```

之后如果需要升级，可以使用 `rustup` CLI命令。

```bash
rustup update

# 如果选择 stable 版本
rustup update stable
```

> 参考: https://www.rust-lang.org/tools/install
