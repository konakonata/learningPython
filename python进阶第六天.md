多线程-threading模块

1.使用threading模块

```python
from threading import Thread
import time

def sayHello():
    print("这是第次说hello")
    time.sleep(1)


if __name__=='__main__':
    for i in range(5):
        #此处不能用sayHello()
        #带括号是调用的函数执行结果，要等结果返回才会继续执行，无法体现多线程。
        thread = Thread(target=sayHello)
        thread.start()
```

2.自定义线程类（继承Thread）

```python
from threading import Thread
import time


class MyThread(Thread):
    def run(self):
        for i in range(3):
            time.sleep(1)
            # name属性中保存的是当前线程的名字
            msg = "I'm " + self.name + '@' + str(i)
            print(msg)


if __name__ == '__main__':
    t = MyThread()#线程1
    t2 = MyThread()#线程2
    t.start()#主线程会等待子线程执行完再结束，为了回收资源
    t2.start()
```

3.线程的执行顺序

线程的执行顺序和进程的执行顺序一样，都是不确定的。

4.线程共享全局变量

```python
from threading import Thread
import time

g_num = 100

def work1():
    global g_num#在局部代码块中调用全局变量
    for i in range(3):
        g_num += 1

    print("in -------work1, g_num is %d"%g_num)#103
def work2():
    global g_num
    print("in --------- work2, g_num is %d"%g_num)#103

print("线程执行前----g_num is %d" %g_num)#100

t1 = Thread(target=work1)
t1.start()

time.sleep(1)

t2 = Thread(target=work2)
t2.start()
```

5.线程共享全局变量的问题

主要是因为线程间的优先级不确定性的问题。

6.同步，解决线程间变量的统一性

互斥锁

 ```python
# 使用互斥锁
from threading import Thread, Lock

g_num = 0


def work1():
    global g_num  # 在局部代码块中调用全局变量

    for i in range(1000000):
        # 这个线程和work2线程都在抢着对这个锁上锁，如果有一方成功的上锁，那么
        # 导致另一方会堵塞（一直等待），直到这个锁被释放
        mutex.acquire()  # 上锁
        g_num += 1
        mutex.release()  # 释放互斥锁，让其他线程抢
    print("in -------work1, g_num is %d" % g_num)  # 103


def work2():
    global g_num

    for i in range(1000000):
        mutex.acquire()  # 双方抢着上锁，如果此处不加，那么就相当于没有用到互斥锁，双方还是随机抢资源
        g_num += 1
        mutex.release()


print("in --------- work2, g_num is %d" % g_num)  # 103

print("线程执行前----g_num is %d" % g_num)  # 100

# 创建一把互斥锁,默认是没有上锁的
mutex = Lock()

t1 = Thread(target=work1)
t1.start()

t2 = Thread(target=work2)
t2.start()
 ```

7.多个线程使用非全局变量

局部变量线程间不通用，互不干涉

```python
# 使用互斥锁
from threading import Thread, Lock
import threading
import time

def work1():
    name = threading.currentThread().name
    print("-------thread1---------%s"%name)
    g_num = 100
    if name == 'Thread-1':
        g_num +=1
        print("work1---------g_num=%d"%g_num)#输出101
    else:
        time.sleep(2)
        print("work1----2----g_num=%d"%g_num)#输出100


t1 = Thread(target=work1)
t1.start()

t2 = Thread(target=work1)
t2.start()
```

8.死锁以及解决办法

加了锁的前提下，两个线程的都在等待对方的锁释放，造成死锁。

```python
from threading import Thread, Lock

import time

g_num = 100


def work1():
    global g_num
    if mutexA.acquire():
        time.sleep(1)
        #如果不用超时时间，因为mutexB被work2上了锁，而work2在等待mutexA的锁释放，此处就会一直堵塞。
        if mutexB.acquire(timeout=2):
            g_num += 1
            mutexB.release()
        print("1111111111111111111111")#会执行
        mutexA.release()


def work2():
    global g_num
    if mutexB.acquire():
        time.sleep(1)
        if mutexA.acquire(timeout=2):#添加超时时间，如果超过设定时间，就跳过执行继续执行
            g_num += 1
            mutexA.release()
        print("2222222222222222222222")#会输出
        mutexB.release()


mutexA = Lock()

mutexB = Lock()

t1 = Thread(target=work1)
t1.start()

t2 = Thread(target=work2)
t2.start()
```

9.同步的应用

同步就是协同步调，按预定的先后次序进行运行。

```python
# 同步的应用
from threading import Thread, Lock
import time

lock1 = Lock()

lock2 = Lock()
lock2.acquire()#进程给lock2上锁，其他线程无法抢夺lock2

lock3 = Lock()
lock3.acquire()#进程给lock3上锁，其他线程无法抢夺lock3


class task1(Thread):
    def run(self):
        while True:
            if lock1.acquire():
                print("task1---------------")
                time.sleep(0.5)
                lock2.release()


class task2(Thread):
    def run(self):
        while True:
            if lock2.acquire():  # lock2已经被进程上锁，只能等task1中的lock2释放锁
                print("task2---------------")
                time.sleep(0.5)
                lock3.release()


class task3(Thread):
    def run(self):
        while True:
            if lock3.acquire():  # lock3已经被进程上锁，只能等task2中的lock3释放锁
                print("task3---------------")
                time.sleep(0.5)
                lock1.release()#释放lock1的锁，让task1执行


t1 = task1()

t2 = task2()

t3 = task3()

t1.start()
t2.start()
t3.start()

```

10.生产者和消费者模式

```python
#生产者和消费者模式
import threading
import time
from queue import Queue

class Producer(threading.Thread):
    def run(self):
        global queue
        count = 0
        while True:
            if queue.qsize() < 1000:
                for i in range(100):
                    count += 1
                    msg = '生产成品' + str(count)
                    queue.put(msg)
                    print(msg)
            time.sleep(0.5)


class Consumer(threading.Thread):
    def run(self):
        global queue
        while True:
            if queue.qsize() > 1000:
                for i in range(3):
                    msg = self.name + "消费了" + queue.get(True)
                    print(msg)
            time.sleep(1)

if __name__ == "__main__":
    queue = Queue()

    for i in range(500):
        queue.put("初始产品" + str(i))
    for i in range(2):
        p = Producer()
        p.start()
    for i in range(5):
        c = Consumer()
        c.start()
```

11.ThreadLocal

字典可以当作全局变量在多线程中使用，不用加锁，把每个线程名当作key，就能保证数据的映射关系不会乱。

而ThreadLocal不用查找dict

```python
import threading

#创建全局ThreadLocal对象
local_school = threading.local()
def process_student():
    #获取当前线程关联的student
    std = local_school.student
    print("hello, %s (in %s)"%(std, threading.currentThread().name))

def process_thread(name):
    #绑定ThreadLocal的student
    local_school.student = name
    process_student()

t1 = threading.Thread(target=process_thread, args={"zfc"}, name="Thread-A")
t2 = threading.Thread(target=process_thread, args={"zxf"}, name="Thread-B")
t1.start()
t2.start()

t1.join()
t2.join()
```

12.异步

```python
#异步调用
from multiprocessing import Pool
import time
import os
def test():
    print("--------------进程池中的进程---pid=%d, ppid=%d"%(os.getpid(), os.getppid()))
    for i in range(3):
        print("----------%d--------"%i)
        time.sleep(1)
    return 'haha'

def test2(args):
    print('-----------callback func---pid=%d'%os.getpid())
    print('-----------callback func---args=%s'%args)
if __name__=="__main__":
    pool = Pool(3)

    # 把test的返回值当作了test2的参数
    #主进程在进行自己的操作时和test形成异步，接收到test的返回值之后优先处理回调函数。
    pool.apply_async(func=test, callback=test2)

    time.sleep(5)

    print("-----朱进程--------pid=%d"%os.getppid())
    pool.close()
    pool.join()
```

13.gil的问题

python多线程实际上是假的多线程，Gil叫做全局解释器，同一时刻，gil保证了多线程中只有一个线程会被cpu调用。

多进程在python中的效率远远大于多线程。

能用多进程就用多进程。

一定要多线程，关键部分用c语言来写。

