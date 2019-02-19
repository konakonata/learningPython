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







