1.工厂模式

```python
class CakeStore(object):
	def __init__(self):
		self.cakeMachine = CakeMachine()
	def createCake(self, cakeName):
		return self.cakeMachine.makeCake(cakeName)


class Cake(object):
	def __init__(self, cakeName):
		self.cakeName= cakeName

class CakeMachine(object):#工厂类
	def makeCake(self, cakeName):
	  return Cake(cakeName)
	def xxxx(self):#只声明，没有具体内容的方法，叫做工厂方法，子类继承后实现具体方法
        pass
```

2.单例模式

__new__()方法

```python
class test(object):
	#初始化功能
	def __init__(self):
		self.num = 100
	#也就是tostring方法
	def __str__(self):
		return "xxxx"
	
	#a=test()先调用__new__()方法创建一个对象，之后再调用__init__()方法来初始化对象
	def __new__(cls): #cls代表当前类，self代表当前实体
		return super().__new__(cls)#借助父类object的__new__()方法，将当前类当作模版传入
	
a = test()
```

```python
class Singleton(object):
	__instance =  none
	__first_init = false
	def __new__(cls, age, name): #因为__init__()方法有参数，创建对象的时候要传参，new方法即使不用参数，但是也要加参数，不然会报错
		if not cls.__instance: #cls就相当于Singleton，直接调用私有类属性
			cls.__instance = super().__new__(cls)
		return cls.__instance
	
	def __init__(self, age, name):#初始化
		if not self.__first_init: 
			self.age = age
			self.nname = name
			Singleton.__first_init = true


a = Singleton(12,"小王")
```

3.异常

3.1 捕获异常

```python
try:#捕获异常
	pass
except"""（异常一，异常三）as 变量名""":#捕获多个异常 变量会存储错误信息
	pass
except"""异常二""":
    pass
```

```python
#捕获所有异常
try:
	pass

except Exception as result:#第二种方式，Exception是所有异常的基类
    print(result)
except:#第一种方式，这种方式必须放在最后
	pass
```

3.2 else关键字

```python
try:
    pass
except NameError as result:
    pass
else: #try中没有异常才会执行else中的代码
    pass
```

3.3finally关键字

```python
try:
    pass
except NameError as result:
    pass
else: #try中没有异常才会执行else中的代码
    pass
finally:#最后肯定会执行的代码
    pass
```

3.4异常的传递

```python
def test1():
    print(a)#NameError异常
	print("111111111111111")#这行代码不会执行，因为上一行代码有异常
def test2():
    test1()
	print("111111111111111")#这行代码不会执行，因为上一行代码有异常
def test3():
    test2()

test2() #因为2调用了1,1的异常就抛给2了，3同理也会报异常，可以在2和3中捕获异常
```

3.5抛出自定义异常

```python
class ShortInputException(Exception):#继承自父类Exception
	#自定义的异常类
    def __init__(self, length, atleast):
        #super.__init__()
        self.length = length
        self.atleast = atleast
	def __str__(self):
        return "请输入至少" + self.atleast +"位字符"
try:
    a = input("请输入=======")
    if len(a) < 3:
        #raise引发自定义异常， raise就是抛出异常，
        raise ShortInputException(len(a), 3)
except EOFError:
    raise #抛出异常，不用加异常类
    print("请输入结束标志 EOF")
except ShortInputException as result:#有问题 要实验
    print(result)
```

4.模块

import关键字用来导入其他模块

例如随机数  import random （其实就是导入random.py文件）

4.1 自定义模块，模块就是py文件（就相当于java中的导入，而python直接用文件名.方法名就可以调用模块中的方法，java必须创建对象才能调用方法）

4.2 本地测试

```python
def test():
	print("aaaaaa")

#测试
if __name__ == "__main__":#__name__如果是本地执行就是__main__如果是被其他文件导入执行 就是脚本名
	print("测试")
	test()
    
import test#导入test.py模块，test中的__name__变量的值就是test，不是__main__，上述的测试代码就不会执行
test.test()
```

4.3省略导入模块名

```python
from random import * #从random模块导入所有
randint(0,2) #此处就不用random.randint(0,2)来调用方法，可以直接使用

from random import randint,randrange #导入多个
```

4.4定位模块

python解析器对模块未知的搜索顺序是：

1.当前目录

2.如果不在当前目录，Python则搜索在shell变量PYTHONPATH下的每个目录

3.如果都找不到，Python会查看默认路径，unix下，默认路径一般为/usr/local/lib/python(python的安装目录)

4.模块搜索路径存储在system模块的sys.path变量中，变量里包含当前目录，pythonpath和由安装过程决定的默认目录

```python
import sys
sys.path.insert(index, path)#把现有目录插入到系统目录列表中，0代表放在最前
```

