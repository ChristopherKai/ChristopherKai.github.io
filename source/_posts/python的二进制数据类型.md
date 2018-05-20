---
title: python的二进制数据类型
tags:
  - python
  - development
date: 2017-11-18 22:30:33
---
#bytes,bytearray,memoryview


##bytes
Bytes 不可变的字节序列 ，可以视作整数数组，比如b[0] 返回一个整数
```python
  b'still allows embedded "double" quotes'
  bytes(10)
  bytes(range(20))
  bytes(obj) #obj 遵循buffer protocol的二进制对象
```
只能用ascii表示（0-255），大于127必须逃逸


##bytearray
bytes 的可变形式。
```python
  bytearray()
  bytearray(10)
  bytearray(range(20))
  bytearray(b'Hi!')
```


两种对象都有类似其他序列对象的api

```python
  a = b"abc"
  b = a.replace(b"a", b"f")
```
