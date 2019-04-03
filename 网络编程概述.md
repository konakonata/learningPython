1.**tcp/ip协议(族) 协议总称**

![1554087096906](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1554087096906.png)

4层划分：链路层(网络接口层) → 网络层(网际层) →传输层 →应用层

2.**端口**

端口是用来区分进程的

端口是通过端口号来标记的，端口号只有整数，范围是从0到65535

知名端口号是众所周知的端口号，范围从0到1023，80端口分配给HTTP服务，21端口分配给FTP服务

动态端口的范围从1024到65535

netstat -an 查看端口号

3.**IP**

网络上电脑的唯一标识

IP中每个数字是一个字节，一个字节占8位。

IP最后一个字节不能用0或者255,0代表网络号，255代表广播

ipv6是用16进制操作，中间用冒号隔开

4.**socket简介(套接字)**

用来不同电脑之间的通信

```python
#创建套接字对象
import socket
#from socket import *
#AF_INET代表ipv4， SOCK_STREAM创建的是tcp socket(tcp套接字)
#SOCK_DGRAM创建的是udp socket(udp套接字),udp快速，但是不稳定，有可能丢失数据
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

```python
from socket import *

s = socket(AF_INET, SOCK_DGRAM)#udp套接字

sendAddr = ("192.168.0.133", 8080)

#python3在发消息时，在消息前面加个b，python2不用
s.sendto(b"db", sendAddr)
```

5.端口问题和端口绑定

端口是唯一的，不允许相同，在一个进程释放端口之前，其他进程无法使用。

```python
from socket import *

# 创建udp套接字
udpsocket = socket(AF_INET, SOCK_DGRAM)

# 绑定本地的相关信息，如果一个网络程序不绑定，则系统会随机分配
bindAddr = ('', 7788)  # ip和端口号，ip一般不用谢，表示本机的任何一个ip，一个电脑上可以有多个ip

#接收方需要绑定，绑定本机的端口，发送方不需要。
udpsocket.bind(bindAddr)  # udpsocket绑定本机端口号
#对数据进行编码
udpsocket.sendto("hha".encode("gb2312"), ("192.168.0.201"))
# 等待接受对方发送的数据
recvData = udpsocket.recvfrom(1024)  # 接收数据，1024标识本次接收的最大字节数

# 显示接受到的数据

print(recvData) #包含消息和发送方的ip和端口
#将数据和发送方的ip、端口分别保存
content, destInfo = recvData
content.decode("gb2312")#解码
udpsocket.close()
```

单工：只能单向发送数据

半双工：同一时刻只能有一方发送数据，但是双向的

全双工：同一时刻双向操作，发送的同时可以接收

6.模仿qq聊天，多线程

```python
#模拟qq聊天，多线程
from socket import *

from threading import Thread


def recvData():
    while True:
        recvInfo = udpSocket.recvfrom(1024)#限制每次接收数据的最大字节
        print()
        print(">>>[%s]%s"%(str(recvInfo[1]), recvInfo[0]))
		

def sendData():
    while True:
        sendInfo = input("<<<", "end")
        udpSocket.sendto(sendInfo.encode("gb2312"), (destIp, destPort))

udpSocket = None
destIp = ""
destPort = 0

def main():
    global udpSocket
    global destIp
    global destPort
    destIp = input("对方的ip：")
    destPort = int(input("对方的port:"))
    udpSocket = socket(AF_INET, SOCK_DGRAM)
    udpSocket.bind(("", 8080))

    tr = Thread(target=recvData)
    ts = Thread(target=sendData)

    tr.start()
    ts.start()

    tr.join()#让进程等待子线程的结束
    ts.join()

if __name__ == "__main__":
    main()
```



