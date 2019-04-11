1.单继承

```python
class Cat(object):#object是小写
    def run(self):
        print("--------------------跑----------------")
class Bosi(Cat):
    pass
class Jiafei(Cat):#里面有一个类就是单继承
    pass

bosi = Bosi()
jiafei = Jiafei()
bosi.run()
```

继承在于将两个类共有的属性和方法封装到一个父类中，通过继承的方式提高代码重复使用率

2.继承时，父类的私有属性/方法的问题

```python
class Cat(object):
    def __init__(self):
        self.__name = "abc"

    def __test(self):
        print("------------------测试---------------")
    def run(self):
        print("--------------------跑----------------")
class Bosi(Cat):
    def test(self):
        self.__test() #报错
        print(self.__name)#报错

bosi = Bosi()
jiafei = Jiafei()
bosi.run()
```

子类无法直接调用父类的私有属性和私有方法

3.重写

```python
class Jiafei(Cat):
    def run(self):#发现继承自父类的方法不适合，可以进行重写
        print("-------边跑边叫----喵喵喵")

```

4.多继承

```python
class Ma(object):
    def pao(self):
        print("===================100km/h--跑--")
	def jiao(self):
        print("-----马在叫------")
class Lv(object):
    def tuo(self):
        print("------驼物品------")
	def jiao("------驴在叫------------")
class Luozi(Ma, Lv):#多继承，骡子继承于马和驴
    pass

luozi = Luozi()
luozi.pao()
luozi.tuo()
luozi.jiao()#如果多个平级父类的方法重名，会优先调用第一个继承的父类的方法，也就是马
print(Luozi.__mro__)#这边是类调用私有属性，输出是元组
#(<class '__main__.Luozi'>, <class '__main__.Ma'>, <class '__main__.Lv'>, <class 'object'>)
#如果是继承的多个父类有很复杂的关系，最终调用规则是mro

```

5.多态

定义时的类型和运行时的类型不同，就称为多态

python是弱类型语言：一个变量可以是多个类型，a=100之后也可以a="aaa",并不会固定

6.访问私有属性的方式

```python
class Test(object):
    def __test(self):
        print("======test------------")

print(dir(Test))

#python把私有方法和私有属性的名改了：'_Test__test'
a = Test()
a._Test__test()#这样调用私有方法就不会报错了
```

7 .类属性，实例属性

**不能通过类名调用实例属性和实例方法**

```python
class Cat(object):
    #类属性
    num = 0
    def __init__(self):
        #实例属性
        self.age=1
        self.num = 100#用实例访问属性，如果实例属性和类属性重名，会优先访问实例属性

map = Cat()

print(map.age)
print(map.num)#用对象访问类属性
map.num = 300 #通过对象修改类属性的值，并不会改变类属性的值，而是给a添加实例属性
print(Cat.num)#用类访问类属性，常用
Cat.num = 400#通过类调用类属性，可以给类属性赋值
```

8.类方法和静态方法

```python
class Test(object):
    #类属性
    num = 0
    #实例方法
    def __init__(self):
        #实例属性
        self.age = 1
    @classmethod #类方法，来修改类属性
    def setNum(cls, newNum):
        cls.num=newNum
    @staticmethod #静态方法，可以直接用类名调用
    def printSomething():
        print("aaaaaaaaa死了")
print(Test.num)
```

