1.类装饰器：

```python
class Test(object):
    __slots__ = ("name")
    def __init__(self,name):
		self.name = name
    def __call__(self):
        print("1111111111111")

t = Test()
t()#只有类里面有__call__()方法时才能这么使用。
```

```python
class Test(object):
    def __init__(self,func):
        print("----初始化")
        print("func name is %s"%func.__name__)
        self.__func=func
    def __call__(self, *args, **kwargs):
        print("装饰器中的功能-------")
        self.__func()

@Test#相当于创建对象 test1=Test(test1)
def test1():
    print("----test----")

test1()#就是调用__call__()方法，test1=Test(test1),test1()就是调用call方法，call方法里面self.__func()是指向test1(),就会调用test1()函数
```

2.元类

在python中类也是对象。

