1.文件

打开文件 f = open(文件名，打开方式('w'---只写，文件不存在则创建，存在则覆盖，'a'----追加到文件末尾，'r'-----只读，只能读取已存在的文件 。如果'w+', "r+","a+"就可读写，其余和不带+一样))，文件打开方式默认是只读

f.close()关闭文件

```python
f = open("D:/英雄时刻/123.txt", 'w')
f.write("我的天啊，这么神奇的吗")
f = open("D:/英雄时刻/123.txt")
print(f.read()) #其中包含换行符
str = f.read()
f.read(size)#指定每次读取的字节数长度
print(str.splitlines())
f.readlines()#读多行，结果是每行为一个元素的列表，每行最后都是换行符“\n”
f.readline()#读一行，如果后面没有数据就都是''
```

```python
f_read = open("D:/英雄时刻/123.txt")#读取目标文件
f_write = open("D:/英雄时刻/123附件.txt",'w') #创建要写入的文件
#第一种
for str in f_read.readlines():
    f_write.write(str);
#第二种
str = f_read.read() #读取文件内容
f_write.write(str)#写入文件
#第三种
while True:
    aStr = f_read.readline()
    if (len(aStr) > 0) :
        f_write.write(aStr)
    else:
        break
f_read.close()#关闭文件
f_write.close()#关闭文件
```
2.指定文件读写起始位置

```python
f.tell()#获取当前读取到的位置
f.seek(偏移量,方向(0---文件开头，1---当前位置，2----文件末尾))
```

3.文件重命名和删除,文件夹相关操作(这两个操作都要引入os包)

```python
import os
#重命名
os.rename(old, new)
#删除文件
os.remove(path)
#创建文件夹
os.mkdir(path)
#获取当前目录
os.getcwd()
#改变默认陌路
os.chdir(path)
#获取当前路径下的目录列表
os.listdir()
#删除文件夹
os.rmdir(path)

```

4.如果全局变量是可变变量，局部变量可以同名，相当于重新定义一个局部变量，不会对全局变量进行修改