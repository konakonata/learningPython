1.wireshark抓包工具使用

![1554342809515](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1554342809515.png)

​				发送方的ip           接收方的ip    协议    数据包长度(字节)

![1554347889698](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1554347889698.png)

筛选，通过ip，协议，端口等方面进行筛选

2.tftp下载

![1554356307672](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1554356307672.png)

```python
#! /usr/bin/env python3
# -*- coding:utf-8 -*- 

from socket import *
import struct #为了实现打包struct.pack()和拆包struct.unpack()数据
import sys

# python3 05-xx.py 192.168.105.125 bb.jpg
def main():


    #server_ip = '192.168.105.125'
    #file_name = 'bb.jpg'

    server_ip = '127.0.0.1'
    file_name = 'test.jpg'
    udp_socket = socket(AF_INET, SOCK_DGRAM)
    server_addr = (server_ip,69)

    #  打包数据
    # !表示网络字节序,H表示2bytes无符号整数,
    #  5s表示长度为5字符串
    #  B表示1byte的无符号整数
    fmt = '!H%dsB5sB' % len(file_name)
    send_data = struct.pack(fmt,1,file_name.encode() ,0,b'octet',0)
    #send_data = struct.pack(fmt,1,file_name ,0,b'octet',0)
    print("------------------------------")
    udp_socket.sendto(send_data,server_addr)

    f =  None # 文件对象
    #上一次blockNum
    lastBlockNum = 0

    # 循环接收和应答
    while True:
        recv_data,peer_addr = udp_socket.recvfrom(1024)
        # 拆包数据
        opcode,blockNum = struct.unpack('!HH',recv_data[:4])
        print("opcode=%s,blockNum=%s"%(opcode, blockNum))
        if opcode == 3: # 表示数据包
            # 写入文件
            # 1打开文件
            # 第一次收到服务器发送数据包
            if blockNum == 1:
                print("打开文件")
                f = open("D:/"+file_name,'wb')

            # 拆出数据
            data_fmt = '!%ds' % (len(recv_data) - 4)
            data_content = struct.unpack(data_fmt, recv_data[4:])
            # 写入文件之前判断写过没有
            # if 这一次blockNum == 上一次blockNum + 1
            if lastBlockNum + 1 == blockNum:
                #print(data_content[0])
                f.write(data_content[0]) # 拆出来是元组,bytes对象,write时候需要str字符串

            # 打包应答数据
            ack_data = struct.pack('!HH',4,blockNum)
            udp_socket.sendto(ack_data,peer_addr) # 不能再给server_addr，因为端口号变了
            # 当应答完毕，更新lastBlockNum
            lastBlockNum = blockNum

            # 如果数据长度小于 2 + 2 + 512 传输结束
            if len(recv_data) < 516:
                print('over')
                f.close()
                udp_socket.close()
                break
        elif opcode == 5:# 出错
            err_num = blockNum
            # 拆出错误信息
            fmt = "!%ds" % (len(recv_data) - 5)
            err_msg = struct.unpack(fmt,recv_data[4:-1])
            print('出错信息:%s' % err_msg)
            udp_socket.close()
            break

if __name__ == "__main__":
    main()

```

