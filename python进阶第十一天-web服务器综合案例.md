1.网络层级复习

链路层(具体传输工具)，网络层(ip就是全球唯一坐标)，传输层(以udp或tcp的方式传输数据)，应用层(双方之间要传达的信息数据)

2.socket编程复习

3.前端引入

html 超文本标记语言，浏览器的主要工作是解析html标签字符串

浏览器内核不一样，决定了解析字符串的速度，和支持解析的标签。

4.http协议基础

底层传输使用的tcp协议

客户端的socket，以及客户端socket和服务端的连接都是浏览器完成的

客户端发送：

GET / HTTP/1.1

1）请求方式，GET-获取资源，POST-修改数据，PUT-新增数据，DELETE-删除数据

OPTION-询问服务器支持的某种特性，HEAD-返回报文头数据

2）请求路径 / 

3)http协议的版本 1.1

host 服务器主机ip

服务端响应：

200 成功，500服务端错误，400用户端错误

5.HTTP通许流程

6.HTTP协议补充说明

Http是无状态的，无法区分同一个客户端两次访问来自同一个客户端。

7.爬虫与搜索引擎

爬虫是一个程序，向特定网站发送http get请求，拿到服务器的页面资源

8.固定返回值的web服务器代码

```python
from socket import *
from multiprocessing import Process

# 定义静态资源的根目录
HTML_ROOT_DIR = './html'


def fun(client_socket):
    # 接收数据
    recv_data = client_socket.recv(1024)
    # 解析HTTP报文数据，request_data
    # 提取请求方式
    # 提取请求路径path
    print(recv_data)
    # 构造返回响应数据
    response_start_line = "HTTP/1.1 200 OK\r\n"
    response_header = "Server: My server\r\n"
    response_body = "hello itcast"
    response = response_start_line + response_header + "\r\n" + response_body
    # send()报文数据
    client_socket.send(bytes(response, "utf-8"))
    # 关闭客户端连接
    client_socket.close()


# TCP端
if __name__ == '__main__':
    socket = socket(AF_INET, SOCK_STREAM)
    socket.bind(("", 8080))
    socket.listen(5)
    while True:
        client_socket, client_addr = socket.accept()
        p = Process(target=fun, args=(client_socket,))
        p.start()
        client_socket.close()
```

9.静态文件的web服务器代码

```python
from socket import *
from multiprocessing import Process
import re

# 定义静态资源的根目录
HTML_ROOT_DIR = './html'


def fun(client_socket):
    # 接收数据
    recv_data = client_socket.recv(1024)
    print(recv_data)
    # 解析HTTP报文数据，request_data
    lines = recv_data.splitlines()
    for line in lines:
        print(line)
    request_start_line = lines[0]
    # 提取请求方式
    # 提取请求路径path
    # group提取第一个括号的字符串
    # 报文数据全是字节类型数据，需要转成字符串
    file_name = re.match(r"\w+ +(/[^ ]*) ", request_start_line.decode("utf-8")).group(1)
    if "/" == file_name:
        file_name = "/index.html"
    try:
        # 打开文件，读取内容
        # r-只读，b-以2进制方式打开
        print(file_name)
        file = open(HTML_ROOT_DIR + file_name, "rb")
    except IOError:
        # 构造返回响应数据
        response_start_line = "HTTP/1.1 404 NOT FOUND\r\n"
        response_header = "Server: My server\r\n"
        response_body = "file not found"
    else:
        file_data = file.read()
        file.close()
        # 构造返回响应数据
        response_start_line = "HTTP/1.1 200 OK\r\n"
        response_header = "Server: My server\r\n"
        # 只能是字符串
        response_body = str(file_data, "utf-8")
    response = response_start_line + response_header + "\r\n" + response_body
    # send()报文数据
    # python3必须转成字节形式的响应，如果是python2 则不用转
    client_socket.send(bytes(response, "utf-8"))
    # 关闭客户端连接
    client_socket.close()


# TCP端
if __name__ == '__main__':
    socket = socket(AF_INET, SOCK_STREAM)
    # 设置socket可以重用
    socket.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    socket.bind(("", 8080))
    socket.listen(5)
    while True:
        client_socket, client_addr = socket.accept()
        p = Process(target=fun, args=(clien_socket,))
        p.start()
        client_socket.close()
```

10.文件打开方式，文本和二进制的区别

在linux，unix，mac操作系统中，\n是换行符，在windows操作系统中\r\n是换行符。

在windows中以b二进制方式写入文件，"\n"不会识别为换行，以文本方式写入，'\n'会被识别为换行符

读取二进制方式写入的\n还是\n，读取文本方式写入的\n会编程\r\n

11.pycharm连接ubuntu

![1561604696983](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1561604696983.png)

![1561604843185](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1561604843185.png)

![1561604802863](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1561604802863.png)

![1561604815873](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1561604815873.png)

11.静态文件服务器补充说明

bg显示后台运行进程，fg把后台进程掉到前台。

12.静态文件web服务器面向对象编程

```python
import socket
import re
from multiprocessing import Process

#封装成对象
class HttpServer(object):
    def __init__(self, port):
        self.server_scoket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server_scoket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        self.server_scoket.bind(("", port))

    def start(self):
        self.server_scoket.listen(128)
        while True:
            client_socket, client_addr = self.server_scoket.accept()
            p = Process(target=self.handle_client, args=(client_socket,))
            p.start()
            client_socket.close()

    def handle_client(self, client_socket):
        HTML_ROOT_DIR = './html'
        request_data = client_socket.recv(1024)
        request_lines = request_data.splitlines()
        request_start_line = request_lines[0]
        file_name = re.match("\w+ +(/[^ ]*) ", str(request_start_line, "utf-8")).group(1)
        if "/" == file_name:
            file_name = '/index.html'
        try:
            file = open(HTML_ROOT_DIR + file_name, 'rb')
        except IOError:
            response_start_line = "HTTP/1.1 404 NOT FOUND\r\n"
            response_header = "Server: My server\r\n"
            response_body = "file not found"
        else:
            file_data = file.read()
            file.close()
            response_start_line = "HTTP/1.1 200 OK\r\n"
            response_header = "Server: My server\r\n"
            response_body = str(file_data, "utf-8")
        response = response_start_line + response_header + "\r\n" + response_body
        client_socket.send(bytes(response, 'utf-8'))
        client_socket.close()


def main():
    httpServer = HttpServer(8080)
    httpServer.start()


if __name__ == '__main__':
    main()
```

