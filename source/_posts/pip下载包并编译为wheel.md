---
title: pip下载包并编译为wheel
date: 2019-01-09 15:17:17
tags: python, pip
---

## 离线下载

> 某些情况下需要将python的二进制包编译成whl文件，方便在其它环境使用。

```shell
mkdir some_module
pip install wheel
pip wheel -w some_moudle module_name
```

> example:

```shell
# 以ansible为例
mkdir ansible_package
pip install wheel
pip wheel -w ansible_package ansible

# 查看编译结果
# ls ansible_package
# Jinja2-2.10-py2.py3-none-any.whl
# asn1crypto-0.24.0-py2.py3-none-any.whl
# paramiko-2.4.2-py2.py3-none-any.whl
# MarkupSafe-1.1.0-cp37-cp37m-manylinux1_x86_64.whl
# bcrypt-3.1.5-cp34-abi3-manylinux1_x86_64.whl
# pyasn1-0.4.5-py2.py3-none-any.whl
# PyNaCl-1.3.0-cp34-abi3-manylinux1_x86_64.whl
# cffi-1.11.5-cp37-cp37m-manylinux1_x86_64.whl
# pycparser-2.19-py2.py3-none-any.whl
# PyYAML-3.13-cp37-cp37m-linux_x86_64.whl
# cryptography-2.4.2-cp34-abi3-manylinux1_x86_64.whl
# setuptools-40.6.3-py2.py3-none-any.whl
# ansible-2.7.5-py3-none-any.whl
# idna-2.8-py2.py3-none-any.whl
# six-1.12.0-py2.py3-none-any.whl
```

> 注意：需要提前安装gcc等工具

## 离线安装

> pip 离线安装包

```shell
pip install --no-index --find-links=/path/to/modules
```