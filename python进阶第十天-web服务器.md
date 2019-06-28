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

select 有最大限制，64位的服务器只能监听2047个客户端的请求。

```python
#select回显服务器
import select
import socket
import sys

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(("", 8080))
server.listen(5)#这个数字在linux中无所谓

inputs = [server, sys.stdin]#sys.stdin也是相当于一个套接字

running = True

while True:
    #调用select方法，阻塞等待
    #可接收，可发送和异常三个列表
    readable, writable, exceptional = select.select(inputs,[], [])

    #数据抵达，循环
    for sock in readable:
        #监听到有新的连接
        if sock == server:
            conn, addr = sock.accept()
            #select监听的socket
            inputs.append(conn)
        elif sock == sys.stdin:
            cmd = sys.stdin.readline()
            running = False
            break
        else:
            #读取客户端发送的数据
            data = sock.recv(1024)
            if data:
                sock.send(data)
            else:
                #移除select监听的socket
                inputs.remove(sock)
                sock.close()
    if not running:
        break
```

7.epoll版服务器

poll解决了select服务器上限的问题，但是还是通过循环的方式来筛选客户端的socket，效率低。

epoll使用事件通知机制，让客户端socket根据已定规则修改自身。

```python
# epoll服务器
import socket
import select

# 创建tcp协议socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 绑定服务器的本地端口
s.bind(("", 7808))
# 设置可以重复使用绑定
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
# 设置监听
s.listen(5)

# 创建epoll对象
epoll = select.epoll()

# 测试，用来打印socket对应的文件描述符
# print(s.fileno())
# pring(select.EPOLLIN|select.EPOLLET)

# 注册事件到epoll中
# epoll.register(fd[,eventmask])
# 注意，如果fd已经注册过，那么会发生异常
# 将创建的socket添加到epoll的事件监听中
# fileno()文件描述符
# select.EPOLLIN    可读事件
# 　select.EPOLLOUT   可写事件
# 　select.EPOLLERR   错误事件
# 　select.EPOLLHUP   客户端断开事件
epoll.register(s.fileno(), select.EPOLLIN | select.EPOLLET)
#创建空字典，用来存储信息
connections = {}
address = {}

# 等待客户端发送请求
while True:
    # epoll进行fd扫描的地方，未指定超时时间则阻塞等待
    epoll_list = epoll.poll()

    # 对事件进行判断
    # fd是文件描述符，events则保存可读或可写的状态
    for fd, events in epoll_list:
        print(fd)
        print(events)
        # 如果socket创建的套接字被激活
        if fd == s.fileno():
            conn, addr = s.accept()
            print("有新的客户到来%s" % addr)

            # 将conn和addr的信息分别保存起来
            connections[conn.fileno()] = conn
            address[conn.fileno()] = addr

            # 向epoll注册连接socket的可读事件
            epoll.register(conn.fileno(), select.EPOLLIN | select.EPOLLET)
        elif events == select.EPOLLIN:
            #从激活fd上接收
            recvData = connections[fd].recv(1024)

            if len(recvData) > 0:
                print("recvData====%s"%recvData)
            else:
                #从epoll中移除该连接的fd
                epoll.unregister(fd)
                #server侧主动关闭该连接的fd
                connections[fd].close()

                print("%s------------off-------"%str(address[fd]))
```

8.协程

协程又称微线程，纤程。

上下文：之前用到的资源和之后要用的资源是固定的。

计算密集型：需要占用大量cpu资源，用多进程

io密集型：需要网络功能，大量的时间在等待网络数据的到来，多线程或协程

```python
#协程的简单实现
import time
def A():
    while True:
        print("-------a---------")
        yield#生成器
        time.sleep(0.5)

def B(c):
    while True:
        print("-----------b-------")
        c.__next__()#python3之后，generator函数没有next(),全部变成了__next__()
        time.sleep(0.5)


if __name__ == '__main__':
    a = A()#a就是生成器
    B(a)
```

greenlet版协程

```python
#sudo pip3 install greenlet 要先安装 pip安装到python2，pip3安装到python3
from greenlet import greenlet
import time


def A():
    while True:
        print("-----A-----")
        gr2.switch()#切换到函数B
        time.sleep(0.5)


def B():
    while True:
        print("-----------B---------")
        gr1.switch()#切换到函数A，从A函数切换过来之后先执行time.sleep(0.5)，然后再次循环，走到此处再次切换到函数A
        time.sleep(0.5)


gr1 = greenlet(A)
gr2 = greenlet(B)

gr1.switch()#进入函数A
```

gevent版协程,在遇到耗时操作时，会自动切换协程

```python
# sudo pip3 install gevent
# coding=utf-8
#协程任务切换
import gevent


def f(n):
    for i in range(n):
        print(gevent.getcurrent(), i)
        #用来模拟耗时操作，不能用time模块的sleep()
        gevent.sleep(1)


g1 = gevent.spawn(f, 5)
g2 = gevent.spawn(f, 5)
g3 = gevent.spawn(f, 5)
g1.join()
g2.join()
g3.join()
```



