---
title: CTF经验总结
tags:
  - Note
  - CTF
  - security
  - 网络安全
---

## 思考方向

### 字符串：

形式：字符串末尾为= 

方向：base64，base32，base16编码

形式：**ocjp{zkirjwmo-ollj-nmlw-joxi-tmolnrnotvms}**

方向：[凯撒密码][6]，[弗吉尼亚密码][6],猪圈密码

形式：**fgkjol-ljimnoml{iw-lnwo-ortsazrmojm-xtlnv}**

方向：栅栏密码

形式：**21232f297a57a5a743894a0e4a801fc3**

方向：16进制解码，MD5解密

形式：**=E6=8A=80=E6=9C=AF=E6=9C=89=E6=B8=A9=E5=BA=A6**

方向：[可打印字符编码][1]。类似百分号编码，代替%为=。百分号编码使用python `urllib.parse.unquote`

形式：+++++ +++++ [->++ +++++ +++<] >++.+ +++++ .<+++ [->-- -<]>- -.+++ +++.<

方向：[brainfuck编程语言][2]



### 图片文件：

    1.用silentEye查隐写术
    
    2.用hxD查二进制内容是否包含flag
    
    3.用binwalk查看是否为多张图片

### 音频文件：
    
    1.silentEye查隐写术
    
    2.频谱分析
    


## 软件工具：

[md5解密][3]

[SilentEye][4]

[hxD][5]

[各种古典密码解密][6]

binwalk进行二进制文件的分析
安装：`sudo apt-get install binwalk`




## 工具脚本

```python
#将16进制编码字符串转为ascii字符串

#python3
import binascii
binascii.unhexlify('666c61677b686578327374725f6368616c6c656e67657d')
参数：16进制字符串，
返回值：bytes

#python2
'666c61677b686578327374725f6368616c6c656e67657d'.decode('hex')

#计算与flag字符串差值
def calDiff(mstr, flag = "flag"):
    res = []
    for (i,c) in enumerate(flag):
        res.append(ord(mstr[i]) - ord(c))
    return res


#猪圈密码解密
import sys
if __name__ == '__main__':
    dic = {'a': 'j', 'b': 'k', 'c': 'l', 'd': 'm', 'e': 'n', 'f': 'o', 'g': 'p', 'h': 'q', 'i': 'r', 's': 'w', 'v': 'z',
           't': 'x', 'u': 'y', 'j': 'a', 'k': 'b', 'l': 'c', 'm': 'd', 'n': 'e', 'o': 'f', 'p': 'g', 'q': 'h', 'r': 'i',
           'w': 's', 'z': 'v', 'x': 't', 'y': 'u'}
    crypto = sys.argv[1]
    plaintext = ''
    for c in crypto:
        if c in dic:
            plaintext += dic[c]
        else:
            plaintext += c
    print(plaintext)

#TODO 按照不同组合数解密栅栏密码
#TODO 凯撒密码解密,rotN

```


其他工具：
python -m http.server

#### 出题办法

1.根据编辑器窗口宽度改变ascii字符显示flag


  [1]: http://www.mxcz.net/tools/QuotedPrintable.aspx
  [2]: https://www.splitbrain.org/services/ook
  [3]: http://www.cmd5.com/
  [4]: https://silenteye.v1kings.io/
  [5]: https://mh-nexus.de/en/hxd/
  [6]: https://cryptii.com/caesar-cipher