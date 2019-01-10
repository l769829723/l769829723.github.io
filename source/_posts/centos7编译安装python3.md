---
title: centos7编译安装python3
date: 2019-01-09 14:40:02
tags:
---

> 安装系统开发工具包

```shell
sudo yum groupinstall -y "Development tools"
```

> 编译Python3

```shell
cd /path/to/python/source/code
./configure --prefix=/path/to/install
make && sudo make install
```

> 可能会遇到的报错

1. 缺少 '_ctypes'
```shell
sudo yum install -y libffi-devel
```

2. zipimport.ZipImportError: can't decompress data; zlib not available

```shell
sudo yum install -y zlib-devel
```

3. pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.

```shell
sudo yum install -y openssl openssl-devel
cd /path/to/your/source/code
./configure --prefix=/path/to/install
make && sudo make install
```
