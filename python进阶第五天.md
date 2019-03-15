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

4.multiprocessing

在windows上创建进程

```python
from multiprocessing import process

```





