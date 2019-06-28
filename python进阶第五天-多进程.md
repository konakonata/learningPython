1.pdb调试

import pdb

python -m pdb some.py

#-m指定模块pdb来运行这个python文件

n --- next 向下执行一行代码

l ---list 显示当前代码执行到哪一步

c ---- continue 继续执行代码，不停顿

b + 行数--- break  在指定行添加断点

clear+第几个断点  ---清楚指定断点、

s ---- 进入调用的函数内部

p ---- 打印某个参数

a ----- args 打印所有参数值

r ----- return 快速执行到函数的最后一行

pdb.run("函数名(args)") 进入调试模式



2.**多任务**

```python
#进程的创建 - fork

#windows引入process
#from multiprocessing import process
#linux引入os
import os
import time
g_num = 1 #两个进程之间的全局变量无法共享
#父进程中fork的返回值，就是刚刚创建的子进程的pid
ret = os.fork() #这是开辟一个新的子进程
print(ret) #主进程的ret是大于0，子进程的ret等于0
if ret==0:
    while True:
        print("===============1-----------" + os.getpid())#获取当前子进程id
        g_num += 1 #结果是101，
        time.sleep(1)
else:
    while True:
        print(g_num)#结果为100，虽然在子进程中操作了g_num,但是两个进程间的变量不会共享，主进程的g_num还是100而不是101
        print("===============2-----------" + os.getppid())#获取主进程的id
        time.sleep(1)

#主进程任务结束，就会退出程序，但是子进程还是在继续执行
```

3.多次fork问题

```python
import os
ret = os.fork()
if ret == 0:
    #子进程
    print("1")
else:
    #主进程
    print("2")
    ret = os.fork()
    if ret == 0:
        #主进程的第二个子进程
        print("33")
    else:
        #主进程
        print("44")
ret = os.fork()#如果在外部新建子进程，那么当前的所有进程都会新建子进程。
if ret == 0:
    #主进程的第三个子进程
    #第二个子进程的新建子进程
    #第一子进程的新建的子进程
    print("11")
else:
    #主进程
    #子进程
    #第二个子进程
    print("22")
```

4.multiprocessing,常用，可以跨平台

在windows上创建进程

```python
from multiprocessing import Process
import time
def test():
    while True:
        print("11111111111")
        time.sleep(1)

#把函数放入进程
if __name__ == "__main__":
    p = Process(target= test)
    p.start()#启动进程
    p.terminate()#结束子进程
    p.close()

while True:
    print("------maun----")
    time.sleep(1)
    
p.join([timeout])#堵塞，等待p进程执行完或等待多少秒，主进程才会继续
```

5.process的子类

```python
from multiprocessing import Process
import time

class NewProcess(Process):
    def run(self):
        while True:
            print("------------1-------------")
            time.sleep(1)
p = NewProcess()

p,start()

while True:
	print("---------main------")
    time.sleep(1)

```

6.进程池

```python
from multiprocessing import Pool
import os, time, random

def worker(num):
    for i in range(random.randint(1, 3)):
        print("------pid=%d---- num=%d" % (os.getpid(), num))
        time.sleep(1)

if __name__ == '__main__':
    pool = Pool(3)  # 定义一个进程池，最大进程数3
    for i in range(10):
        #每次循环将会用空闲出来的子进程去调用目标
        #如果添加的数量超过了进程池中进程的个数的话，那么不会导致添加不进入。添加到进程中的任务，如果还没有被执行的话那么此时，他们会等待进程池中的进程完成一个任务后，会自动去用刚刚的那个进程，完成当前新任务。
        pool.apply_async(worker, (i,))  #以元组的方式传值
    pool.close()  # 关闭进程池，关闭后pool不在接收新的请求
    pool.join()  # 等待pool中所有子进程执行完成，必须放在close语句之后

```

7.进程间通信--Queue(队列)

```python
#coding=utf-8
from multiprocessing import Queue

q = Queue(3)#初始化一个Queue对象，最多可接收三条put消息

q.put("消息1")
q.put("消息2")
print(q.full())#false
q.put("消息3")
print(q.full())#true
q.put("消息4")#队列已满，再put，默认时堵塞的，会等待空出队列再put
#因为消息队列已满，下面的 try都会捕获异常，第一个try会等待2s后再抛出异常
#第二个try会立刻抛出
try:
    q.put("消息4",True,2)
except:
    print("消息队列已满，现有消息数量：%s",q.qsize())

try:
    q.put_nowait("消息4")
except:
    print("消息队列已满，现有消息数量：%s",q.qsize())

#推荐方式，先判断是否满，再put
if not q.full():
    q.put_nowait("消息4")
#读取消息时，先判断是否为空，再读取
if not q.empty():
    for i in range(q.qsize()):
        print(q.get_nowait())
```

```python
#进程通信，读取子进程读取写入子进程写入的数据
from multiprocessing import Process, Queue
import os, time, random


def write(q):
    for value in ['a', 'b', 'c']:
        print("put %s into queue" % value)
        q.put(value)
        time.sleep(random.random())


def read(q):
    while True:
        if not q.empty():
            value = q.get(True)  # 读取值之后，队列中也就没了
            print("get %s from queue" % value)
            time.sleep(random.random())
        else:
            break


if __name__ == '__main__':
    q = Queue()
    # 先调用写入队列的子进程
    pw = Process(target=write,args=(q,))#传参方式
    pw.start()
    pw.join()

    # 再调用读取队列的子进程
    pr = Process(target=read, args = (q,))#传参方式
    pr.start()
    pr.join()

```

8.进程池中的Queue

```python
#进程池中的Queue
from multiprocessing import Manager,Pool
import os,time,random

def reader(q):
    print("reader启动(%s)，父进程为(%s)"%(os.getpid(),os.getppid()))
    for i in range(q.qsize()):
        print("reader从Queue获取到消息:%s"%q.get(True))

def writer(q):
    print("writer启动(%s),父进程为(%s)"%(os.getpid(),os.getppid()))
    for i in "zhangfucheng":
        q.put(i)


if __name__=="__main__":
    print("(%s) start"%os.getpid())
    q = Manager().Queue()#使用Manager中的Queue来初始化

    po = Pool()
    #使用阻塞模式创建进程，这样不需要再reader中死循环了，可以让writer完全执行完后，再用reader

    po.apply(writer, (q,))#apply方法是阻塞的。就是等待当前子进程执行完毕后，在执行下一个进程。
    po.apply(reader, (q,))#apply_async 是异步非阻塞的。
    po.close()
    po.join()
    print("(%s) End"%os.getpid())
```

9.多进程拷贝文件

```python
from multiprocessing import Pool
import os


# 复制文件的功能函数
def copyFileTask(name, oldFloderName,newFloderName):
    os.chdir(oldFloderName)
    fr = open(name)
    os.chdir(newFloderName)
    fw = open(name, 'w+')
    content = fr.read()
    fw.write(content)
    fr.close()
    fw.close()

def main():
    oldFloderName = input("请输入文件夹的名字：")

    newFloderName = oldFloderName + "-复件"
    # 新建文件夹
    os.mkdir(newFloderName)
    # 获取旧文件夹所有文件名
    fileNames = os.listdir(oldFloderName)
    print(fileNames)

    # 使用多进程的方式复制文件

    pool = Pool(5)
    for name in fileNames:
        pool.apply_async(copyFileTask, (name, oldFloderName, newFloderName))


if __name__ == "__main__":
    main()

```



