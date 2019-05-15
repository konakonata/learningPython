1.udp(用户数据包协议)广播(一对所有)

只有udp有广播，tcp没有

```python
#coding=utf-8
import socket, sys

#<briadcast>是通用性的
dest = ("<broadcast>", 7788)#某个应用的端口

#创建udp套接字
udpScoket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

#对这个需要发送广播数据的套接字进行修改设置，否则不能发送广播数据
udpScoket.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, 1)

#以广播的形式发送数据到本网络的所有电脑中
udpScoket.sendto("Hi", dest)

print("等待对方回复，（按crtl+c退出）")

while True:
    (buf, address) = udpScoket.recvfrom(2048)
    print("recived from %s : %s" %(address, buf))
```

2.tcp(传输控制协议)相关介绍

创建套接字(<u>socket创建的套接字，默认是主动套接字，即是默认是发消息的套接字</u>)，绑定本地端口，监听(listen)(<u>将套接字转成被动模式，能够接收消息</u>)，接收消息(accept)

```python
# coding=utf-8
# 服务器
import socket

# 创建服务器的socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 绑定本地端口
s.bind("", 8080)
# 创建监听，5表示同一时刻最多5个客户端给服务器发送消息
s.listen(5)

# 等待接收数据
# newSocket表示新的客户端，clientAddr 表示新客户端的ip以及端口
# accept具有阻塞作用，在等待客户端发消息
newSocket, clientAddr = s.accept()
# 接收数据
recvData = newSocket.recv(1024)
#如果接收到的数据长度为0，就说明客户端断开连接，可以关闭客户端socket
print("%s,%s" % (str(clientAddr), recvData))

newSocket.close()
s.close()
```

```python
# coding=utf-8
# 客户端创建

from socket import *

# 创建socket
clientSocket = socket(AF_INET, SOCK_STREAM)

# 建立连接
sendAddr = ("192.168.0.138", 8080)
clientSocket.connect(sendAddr)

# 提示用户输入数据
sendData = input("请输入要发送的数据：")
# 发送数据
clientSocket.send(sendData.encode("gb2312"))

# 接收服务端发送的数据，最大接收1024个字节
recvData = clientSocket.recv(1024)

print("接收到的数据%s" % recvData)

# 关闭套接字
clientSocket.close()
```

```python
#多进程收发消息
# coding=utf-8
# 服务器
import socket
from multiprocessing import  Process

def clientDeal(newSocket):
    while True:
        # 接收数据
        recvData = newSocket.recv(1024)
        if len(recvData) == 0:
            break
        else:
            print("recvData = %s" % recvData)
    newSocket.close()


if __name__ == '__main__':
    # 创建服务器的socket
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 绑定本地端口
    s.bind("", 8080)
    # 创建监听，5表示同一时刻最多5个客户端给服务器发送消息
    s.listen(5)

    # 等待接收数据
    # newSocket表示新的客户端，clientAddr 表示新客户端的ip以及端口
    # accept具有阻塞作用，在等待客户端发消息
    while True:
        newSocket, clientAddr = s.accept()
        p = Process(target=clientDeal, args=(newSocket,))
        p.start()
        p.terminate()
        p.close()
```

3.组网

网络号是ip和网络掩码按位与的出来的。

两台电脑之间通信的前提是在同一网段内。

电脑之间通信是通过电信号控制的，多台电脑直接不能把网线剪开链接在一起，这样会造成数据混乱。光信号不知道是什么情况。

两台电脑可以直接连线通信，三台或者多台电脑之间通信则需要连接到集线器。集线器能够实现多台电脑链接，实现小型局域网，收到的所有数据包都会通过广播的形势发出。

交换机则是会指向发送数据包，现在集线器已被淘汰。

ping命令走得icmp协议，先要发一个arp协议数据包，获取接收方的mac地址。

mac地址就是硬件地址，是网卡的序列号。

arp协议是获取一个电脑上的网卡号，就是mac地址。

arp -a 获取通信的mac地址

路由器组网：

跨网络不可以直接通信，而路由器可以实现不同网络间的通信。路由器内置网卡。

路由器之间通过路由协议进行通信。

在通信过程中， 数据包的目标ip不会变，而mac地址会一直变。

dns协议-解析域名的协议，dns服务器是udp协议。

访问百度过程：1,先解析出baidu.com对应的ip地址，2,使用arp协议获取默认网关的mac地址，3,数据发送给默认网关，4,默认网关将数据转发给路由器，5,路由器根据自己路由协议，来选择一个合适的较快的路径。转发数据给目的网关，6,dns服务器查询解析baidu.com对应的ip地址，并将原路返回给请求这个域名的client，7,得到baidu对应的ip地址后，会发送tcp的3次握手，进行连接。8 使用htto协议发送请求数据给web服务器  9 web服务器接收数据请求之后，通过查询自己的服务器得到相应的结果，原路返回给浏览器  10 浏览器渲染数据  11 浏览器关闭tcp连接，即4次挥手，完成整个访问过程。

DHCP协议，能够发现当前局域网中没有ip的电脑，并分配ip。



4.tcp三次握手

第一次握手，客户端发送请求数据发送给服务器，SYN标记了第一次握手，ACK的值为0，序号为0.

ACK是确认包。

第二次握手，服务器接收到第一次握手的请求数据，ack的值变为1，序号还是0，SYN+ACK标记了第二次握手，客户端确认ack的值

第三次握手，客户端确认ack的值为1后，再次发送请求数据，ACK标记第三次握手，序号变为1

三次握手之后，连接建立完成。

http请求紧接着第三次握手的数据包发送给服务器。服务器接收到http请求后，会先发一个确认收到请求的ack发送给客户端，然后把http请求返回数据发送给客户端，客户端收到数据后会再次给服务器发个确认包。

四次挥手

客户端接收请求的返回值后，会发送关闭数据包，final+ack，服务器接收到之后，返回确认数据和服务器close的确认包，客户端确认后，向服务器发送ack包，结束本次请求。

udp协议无论什么数据都会直接接收，不会确认。所以tcp比udp稳定。

5.长连接和短链接

在一次三次握手之后，多次的发送请求接收数据，然后进行1次4次挥手，称为长连接。

