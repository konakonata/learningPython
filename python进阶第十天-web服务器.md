1.tcp的10中状态

![1555664041933](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1555664041933.png)

2.ttl值表示经过的路由器的个数

2msl，2倍的（数据包在网络上最长存活时间），在此期间内，如果服务器先关闭，会造成套接字绑定的端口不会释放，要等待2-4分钟。

3.listen的队列长度

```python
# coding=utf-8
from socket import *
from time import sleep

# 创建socket
tcpSocket = socket(AF_INET, SOCK_STREAM)

# 绑定基本信息
tcpSocket.bind(("", 8080))

connNum = int(input("请输入最大连接数："))

# 使用socket创建的套接字默认的属性是主动的，使用listen将其变为被动，这样就可以接收别人的连接
tcpSocket.listen(connNum)

while True:
    # 如果有新的客户端来连接服务器，那么就产生一个新的套接字专门为这个客户端服务器
    newSocket, clientAddr = tcpSocket.accept()
    print(clientAddr)
    sleep(1)

# 客户端的代码
from socket import *

connNum = input("请输入要连接服务器的次数：")
for i in range(int(connNum)):
    s = socket(AF_INET, SOCK_STREAM)
    s.connect(("127.0.0.1", 8080))
    print(i)
```

在linux操作系统中，listen中的连接个数不存在限制，连接个数是由linux系统内核决定的。在windows等系统中，listen中的数字是多少，就决定了同一时间最大的连接数。

4.网络攻击

tcp半链接攻击，发送大量伪造syn数据攻击，在接收服务器的确认后，不进一步向服务器发送确认包，造成服务器内存满溢，正常用户无法访问。又称ddos攻击或syn洪水攻击。

dns攻击，dns劫持，攻击服务器后，把域名绑定的ip更换，客户端访问后，就会访问黑客指定的ip。

dns欺骗，不断的发送假的域名解析ip给客户端，达到欺骗的目的。

查看域名解析的ip地址方法

nslookup 域名

arp攻击，伪造假的mac和ip数据包发送给路由器，路由器接收到客户端发送的数据后，会先发给黑客，然后黑客将数据保存之后，在发送给路由器，路由器发送的数据经由交换器发送给黑客，然后黑客在发送给客户端。

5.多进程或多线程服务器

```python
from socket import *
from multiprocessing import Process
from threading import Thread


def main():
    tcpSocket = socket(AF_INET, SOCK_STREAM)
    # 重复使用绑定的信息，在服务器先关闭的情况下，不用等待，可以再次绑定此端口和ip
    tcpSocket.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    tcpSocket.bind(("", 8080))
    tcpSocket.listen(5)
    try:
        while True:
            newSocket, destAddr = tcpSocket.accept()
           # t1 = Thread(target=dealClient, args=(newSocket, destAddr))
            #t1.start()
            #因为线程共享这个套接字，如果关闭了这个套接字，其后的客户端就不能调用此套接字
            #因为对象实例存放在主内存中，线程只是不同的引用，而不会新建。
            #newSocket.close()
            p = Process(target=dealClient, args=(newSocket, destAddr))
            p.start()
            p.terminate()
            p.close()
            newSocket.close()
    finally:
        tcpSocket.close()
def dealClient(newSocket, destAddr):
    while True:
        recvData = newSocket.recv(1024)
        if len(recvData) > 0:
            print("接收到的数据%s"%recvData)
        else:
            print("客户端关闭")
            break


if __name__ == '__main__':
    main()
```

单进程服务器不现实，效率太低，只能在一次处理一个客户端的请求。

子进程在使用的时候才会去拷贝父进程的数据。

6.select版服务器

select版服务器可以批量处理大量客户端的连接请求，同时筛选出包含数据的请求，返回给应用程序。

