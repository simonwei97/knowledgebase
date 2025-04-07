# 镜像源配置

## Pypi

### pip安装

```bash
# 临时使用镜像源
pip install {pkg} -i https://pypi.tuna.tsinghua.edu.cn/simple

# 永久配置为默认源
python -m pip install --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

```

### Poetry包管理

```bash
# 设置首选镜像
poetry source add --priority=primary mirrors https://pypi.tuna.tsinghua.edu.cn/simple/

# 设置补充镜像
poetry source add --priority=supplemental mirrors https://pypi.tuna.tsinghua.edu.cn/simple/
```

> [TUNA PyPI 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)