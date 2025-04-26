## 1. Python (UV)

`uv` 工具介绍: [https://docs.astral.sh/uv/](https://docs.astral.sh/uv/)

```bash
# 下载安装
curl -LsSf https://astral.sh/uv/install.sh | sh

# 初始化
cd workspace
# 初始化
#  -p: 指定 python 版本
uv init -p 3.xx

# 创建本项目的虚拟环境 virtual environment,
#  -p: 指定 python 版本
uv venv -p 3.xx

# 激活当前虚拟环境
source .venv/bin/activate
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
# 升级
rustup update

# 如果选择 stable 版本
rustup update stable
```

> 参考: [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install){target="_blank"}


## 4. Java

- JDK下载: [https://jdk.java.net/archive/](https://jdk.java.net/archive/){target="_blank"}
- Maven包(包括 `mvnd` )下载: [https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi){target="_blank"}

```bash
# ~/.bash_profile
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-17.0.2.jdk/Contents/Home
export MAVEN_HOME=/opt/maven/apache-maven-3.9.9
export PATH=$PATH:$MAVEN_HOME/bin
```

- Maven加速: 
    - 阿里云: [https://developer.aliyun.com/mvn/guide](https://developer.aliyun.com/mvn/guide){target="_blank"}

```bash
vim /opt/maven/apache-maven-3.9.9/conf/settings.xml
```

在 `<mirrors>xxx<</mirrors>>` 中添加 aliyun Maven加速库

```xml
<mirrors>
  ......
  <mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>aliyun</name>
    <url>https://maven.aliyun.com/repository/public</url>
  </mirror>
  ......
</mirrors>
```