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

```python
class Person(object):
    print("------person_set____")
    def __init__(self):
        self.name="laowang"
```

除了class还可以使用type创建类

```python
#type(类名，由父类名称组成的元组，包含属性的字典)
Person = type("Person", (), {"num": 0});#元类就是用来创造class的type表达式
t1 = Person()
print(t1.num)
print(type(t1))
#添加方法
def printNum(self):
    print("num=%d"%self.num)

Test = type("Test",(),{"printNum":printNum})#这里的printNum是上面函数的引用

t2 = Test()
t2.num = 10
t2.printNum()#输出num=10
```

```python
Cat = type("Cat", (Animal,),{"num":0}) #中间是父类的元组，如果只有一个要加个逗号
Cat.__class__ #输出type
type.__class__#输出type
cat = Cat()
cat.__class__#输出__main__.Cat
```



3.__metaclass__属性

```python
#自定义元类
def upper_attr(function_class_name, function_class_parents, function_class_attr):
    newAttr = {}
    for name,value in function_class_attr.items():
        if not name.startswith("__"):
            newAttr[name.upper()] = value
    return type(function_class_name, function_class_parents, newAttr)


class Foo(object, metaclass=upper_attr): #python3的写法
    #python2的写法 __metaclass__ = upper_attr
    bar = "bip"


print(hasattr(Foo, "bar")) #false
print(hasattr(Foo, "BAR"))#true

f = Foo()

print(f.BAR)
```

4.垃圾回收

```python
#小整数对象池[-5,257) 这些整数对象是提前创建好的，所有这个范围中的整数使用的都是同一个对象
#大整数对象池,每一个大整数，均创建一个新的整数对象
#intern方法    如果字符串不包含特殊字符，那么相同的字符串的不同变量都是指向同一个对象，如果其中包含特殊字符，每一个字符串变量就是一个新的对象
```

引用计数：有一个引用指向某个内存空间，计数就加一

是python垃圾回收机制之一，是主要方式

引用计数无法处理循环引用

```python
import gc
class ClassA():
    def __init__(self):
        print("object born, id : %s"%str(hex(id(self))))


def f2():
    while True:
        c1 = ClassA()
        c2 = ClassA()
        c1.t = c2 #c1的t属性指向c2
        c2.t = c1 #c2的t属性指向c1
        del c1 #删除c1的引用，但是c2.t这个指向还是存在，引用计数不为0
        del c2

gc.disable() #让自动回收失效
f2()	
```

python的隔代回收解决循环引用的问题;

引用计数机制

![1552359719460](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1552359719460.png)

1.在0代链表引用超过700会自动调用垃圾回收，

2.在程序结束后，会调用垃圾回收

3.引入gc包，调用gc.collect()方法，调用垃圾回收

类的常用函数

![1552360531779](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1552360531779.png)

```python
class Tclass(object):
    def __init__(self, subject1):
        print("log subject1=%s"%subject1)#输出python
        self.subject1 = subject1
        self.subject2 = "abc"

    #属性拦截器，item就是类的属性名
    def __getattribute__(self, item):
        if item == "subject1":
            print("log subject1=%s"%item)
            return "def" #如果属性名等于subject1，那么就把subject1赋值def
        else:
            return object.__getattribute__(self,item) #如果不是subject1属性，就返回原有值

    def show(self):
        print("this is Tclass")

s = Tclass("python")
print(s.subject1)
print(s.subject2)
s.show()#也会被属性拦截器拦截
```

5.内建方法

```python
#map函数
#map(function, sequence[,x,....])->list
#function是一个函数
#sequence是一个或多个序列，取决于function需要几个参数
#返回值是一个list
#函数需要一个参数
map(lambda x : x*x, [1,2,3])
#结果为[1,4,9]

#函数需要2个参数
map(lambda x,y : x+y, [1,2,3],[4,5,6])
#结果为[5,7,9]

def f1(x,y):
    return (x,y)
l1 = [0,1,2,3,4,5,6]
l2 = ['sun','m','t','f','s']
l3 = map(f1, l1,l2)
print(list(l3))
#结果为[(0, 'sun'), (1, 'm'), (2, 't'), (3, 'f'), (4, 's')]

#filter函数
#对制定数据进行过滤
#filter(function, sequence)
#function接收一个参数，返回布尔值，true或False,对条件为True保留
filter(lambda x : x%2, [1,2,3,4])
#结果[1,3]

filter(None, "she")
#None就是不过滤

#reduce函数
from functools import reduce
reduce(lambda x,y : x+ y, [1,2,3,4])
#结果为10
reduce(lambda x,y : x+ y, ['a', 'b', 'c'],'d')
#结果为'dabc'

#sorted()函数，将序列排序，默认升序
sorted([1,3,232,424,321], reverse=1)#倒序
```

6.集合set

集合与列表/元组类似，可以存储多个数据，但是不能重复

集合对象还支持union，intersection（交），difference(差)和sysmmetric_difference(堆成差集)等数学运算

```python
#去重
a = [1,1,2,3,4,5,5,6,7,7,7,8]
b = set(a)
a = list(b)
#a的结果是[1,2,3,4,5,6,7,8]

a = 'abcdef'
b = set(a)

A = 'bdf'
B = set(A)
b&B#取交集
#{'b','d','f'}
b|B #取合集，但是不能重复
b-B#取差集
b^B#对称差集，取两个互相差集的结果
```

7.functiontools

partial函数，偏函数

```python
import functools
def showArgs(*args, **kw):
    print('args=====' + args.__str__())
    print('kw=========' + kw.__str__())

p1 = functools.partial(showArgs, 1,2,3)
p1()
p1(4,5,6)
p1(a='python', b = 'itcast')
#结果args=====(1, 2, 3)
#kw========={}
#args=====(1, 2, 3, 4, 5, 6)
#kw========={}
#args=====(1, 2, 3)
#kw========={'a': 'python', 'b': 'itcast'}
```

wraps()函数

```python
import functools
def note(func):
    "note function"
    @functools.wraps(func)
    def wrapper():
        "wrapper function"
        print("note something")
        return func()
    return wrapper

@note
def test():
    "test function"
    print("i am test")

print(help(test))
#test()
#    test function
#None
```

8.模块module

hashlib

```python
import hashlib
m = hashlib.md5()
print(m)
m.update(b'itcast')
print(m.hexdigest())
```

常用扩展库

![1552375202195](C:\Users\kona\AppData\Roaming\Typora\typora-user-images\1552375202195.png)

运行静态服务器

python2中：python -m simpleHTTPServer PORT



python3中: python -m http.server port