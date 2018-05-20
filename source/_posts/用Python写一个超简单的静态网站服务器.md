---
title: 用Python写一个超简单的静态网站服务器
tags:
  - Note
  - python
  - http
---


```python
import socket, os,os.path as path
import urllib.parse

HOST = ''
PORT = 8000
# 把hexo的public文件夹放在同目录下即可
ROOT_DIR = r'public'
# 因为很简单。。所以http 头部除了关键部分，都写死了。。

res_str = '''HTTP/1.1 200 OK\r
Date: Sat, 10 Feb 2018 12:38:32 GMT\r
Server: MySimpleServer\r
Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT\r
Accept-Ranges: bytes\r
Cache-Control: max-age=86400\r
Expires: Sun, 11 Feb 2018 12:38:32 GMT\r
'''

http_300_res_str = '''HTTP/1.1 404 File Not Found\r
Date: Sat, 10 Feb 2018 12:38:32 GMT\r
Server: MySimpleServer\r
Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT\r
Accept-Ranges: bytes\r
Cache-Control: max-age=86400\r
Expires: Sun, 11 Feb 2018 12:38:32 GMT\r
'''


ext_to_mime = {
    '.html': 'text/html',
    '.js': 'text/javascript',
    '.css': 'text/css',
    '.jpg': 'image/jpg',
}


def makeResponse(req_path):
    req_path = urllib.parse.unquote(req_path)
    #请求目录则返回目录下的index.html，请求文件则返回文件
    req_path = req_path + 'index.html' if req_path.endswith(r'/') else req_path
    req_f_path = ROOT_DIR + req_path
    # print(req_f_path)
    res_bytes = bytearray()
    res_bytes.extend(bytes(res_str, 'utf-8'))
    # File Not Found 返回404
    if not os.path.exists(req_f_path):
        res_bytes.extend(bytes(http_300_res_str, 'utf-8'))
        return res_bytes

    with open(req_f_path,'rb') as f:
        stat = os.stat(req_f_path)
        file_size = stat.st_size
        extension = path.splitext(req_f_path)[1]
        mime = ext_to_mime[extension]
        append_headers = "Content-Length: {0}\r\nContent-Type: {1}\r\n\r\n".format(file_size,mime)
        res_bytes.extend(bytes(append_headers, 'utf-8'))
        file_content = f.read()
        res_bytes.extend(file_content)

    return res_bytes




def main():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen(1)
        while True:
            conn, address = s.accept()
            with conn:
                print('Connected by', address)
                data = bytearray()
                while True:
                    recvd = conn.recv(10240)
                    data.extend(recvd)
                    if not recvd: break
                    req = str(data)
                    headers = req.split(r'\r\n')
                    start_line = headers[0]
                    req_path = start_line.split()[1]
                    res = makeResponse(req_path)
                    conn.sendall(res)
                    data = bytearray()




if __name__ == '__main__':
    main()
```
