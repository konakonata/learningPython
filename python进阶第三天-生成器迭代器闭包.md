 生成器：一边循环一边计算的就叫生成器，generator

```python
from collections import Iterable
#生成器
#使列表中的每个元素都加一
#简单
a = [1,2,3,4,5,6,7]

for index, i in enumerate(a):
    a[index] += 1

print(a)
#一般
#b = map(lambda x:x+1, a);
#print(b);
#for i in b:
 #   print(i);

#高级-列表生成式
c = [i+1 for i in a];
print(c)

#生成器就是把列表生成式的[]变成()
generator_ex = (i+1 for i in a);
print(generator_ex)
#生成器属于迭代器对象，可以调用next方法
#generator保存的是算法，每次调用next(generaotr_ex)就计算出他的下一个元素的值，
#直到计算出最后一个元素，没有更多的元素时，抛出StopIteration的错误
print(next(generator_ex))

#通常还是使用for循环的方式遍历生成器
for i in generator_ex:
    print(i);

print("=--------------------------")
#斐波那契数列
def fib(max):
    a,b,n = 0,1,0;
    while n < max:
        a,b = b, a+b;
        print(a);
        n = n + 1;
fib(10)
print("-yield-------------------------")
#yield生成器
def fib1(max):
    a,b,n = 0,1,0;
    while n < max:
        yield b;
        a,b = b, a+b;
        n = n + 1;
a = fib1(10);#fib1(10)结果是一个生成器对象，只有使用next()或者for循环遍历生成器才会调用函数。
print(isinstance(a,Iterable));
for i in a:
    print(i)

#另一个yield例子
def read_file(fpath):
    BLOCK_SIZE = 1024
    with open(fpath, 'rb') as f:
        while True:
            block = f.read(BLOCK_SIZE)
            if block:
                yield block
            else:
                return
```

send的使用：

```python
def test():
    i = 0
    while i < 5:
        temp = yield i
        print(temp)
        i += 1
t = test()#t就是个生成器了
t.__next__()#print(temp)结果为none
t.send("hah")#print(temp)的结果为hah
#__next__()和send()都可以遍历生成器的值，但是后者可以当作yield i的返回值给temp赋值
```

完成多任务：协程

```python
def test1():
    while True:
        print("-----11------")
        yield None#起到终止循环的作用
def test2():
    while True:
        print("-----22------")
        yield None
t1 = test1()
t2 = test2()
while True:
    t1.__next__()
    t2.__next__()
```



1.迭代器：

可迭代对象：

一类是集合数据类型：如list，tuple，dict，set，str等

一类是generator，包括生成器和带yield的generator function。

这些可以统称为可迭代对象：Iterable

判断是否可以迭代

```python
from collections import Iterable

isinstance("abc", Iterable)#第一个参数是实例，可以迭代返回true
```

判断是否是迭代器对象

```python
from collections import Iterator
isinstance("abc", Iterator)#返回false
#如果实例不是迭代器对象
#要用iter()函数把他变成迭代器实例
a = [11,22,333,44]
iter(a)
next(a)#依次取出迭代器中的值
```

2.闭包

函数的引用

```python
def test():
    print("-------------------1--")

#第一种引用
test()
#第二种引用
b = test
#输出：<function __main__.test>
b()#两种引用结果一致
```

什么是闭包

函数里面再定义函数，内部函数使用了外部函数的参数，这个整体成为闭包

```python
#闭包模型
def test(num):
    #内部函数
    def test_in(num2):
        num1 = num + num2
        return num1
    return test_in
ret = test(100)#返回值：<function __main__.test_in>
ret1 = ret(1)#调用test_in
print(ret1)#结果为101
	
```

闭包的使用实例

```python
#使用实例，能够简化传参，让参数的传递更加多样化
def test(a,b):
    def test_in(x):
		return a*x + b
    return test_in

ret = test(10,11)
#输出10 * 12 + 11
print(ret(12)):
```

3.装饰器

开发要遵循开放封闭的原则；

开放：对扩展开发

封闭：不改动已实现的功能代码块

```python
# 在现有功能函数f1()中验证权限，有权限的才能调用f1(),w1()就是装饰器
def w1(func):  # func函数的地址
    print('-----------5----------')
    def inner():
        # 验证
        # 验证
        print("--------1——------------")
        return "<b>" + func() + "</b>"

    return inner


def w2(func):  # func函数的地址
    print('-----------4----------')
    def inner():
        # 验证
        # 验证
        print("--------2——------------")
        return "<i>" + func() + "</i>"  # 调用传进来的函数

    return inner


@w1  # @w1就相当于w2 = w1(w2)
@w2 #@w2就相当于f1 = w2(f1)
def f1():
    print("--------------3----------")
    return "hello,world"


print(f1())

#输出结果：
#-----------4---------- #4和5的位置说明，最先装的装饰器是w2，然后装饰w1
#-----------5----------
#--------1——------------
#--------2——------------
#--------------3----------
#<b><i>hello,world</i></b>
#1,2,3说明多个装饰器 f1()调用w1()，w1()中的func()调用w2(),也就是w2()中inner()的返回值,w2()中的func()调用f1();
```

@w1注解就等于 f1 = w1(f1) ，即使没有调用f1() 装饰器也会执行f1 = w1(f1)也就是调用w1();

而不是等到调用f1()才去装饰。

```python
#有参装饰器
def func(func):
    def inner(*args):#装饰函数有几个参数，内部函数也要有相应的参数
        print("---------11-----")
        func(*args)#解决不定长参数的问题
        print("----------222-----")
    return inner
@func
def test(a,b):
    print("---a=%d----b=%d"%(a,b))
test(11,22)
    
```

```python
#通用装饰器
def func(func):
    def inner(*args,**kwargs):
        return func(*args,**kwargs)
```

```python
#带有参数的装饰器
def time_func(pre):
    print("-------11------")
    def timeFunc(func):
        print("-------22------")
        def inner():
            print("-------33------")
            ret = func()
            print("------pre=%s, arg=%s"%(pre, ret))
        return inner
    return timeFunc
@time_func("abc")#带参数的装饰器，要先调一个函数，将里面装饰器的医用当作返回值返回。
def test():
    return "bcd"

test()#输出结果:-------11------ -------22------ -------33------ ------pre=abc, arg=bcd
```

3.作用域：globals()函数能打印出当前所有全局变量，locals()打印所有当前局部变量；

如果局部变量和全局变量名称一样，方法内使用的变量肯定就是局部变量。

legb查找规则：locals局部变量 - enclosing function 闭包函数 --- globals全局变量 ---- builtins python默认导入的包



4.python语言的动态性：

```python
# 给对象添加属性
class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age


laowang = Person("lao", 18)
# 可以动态的添加属性
laowang.address = "北京"
# 老王动态加的属性只适用于老王这个对象，对于这个类的其他对象不适用
laozhao = Person("laozhao", 20)
# 动态添加类属性
Person.phone = '1900'
#print(laozhao.address)#这个地方会报错，因为laozhao没有address属性
print(laowang.name)
print(laowang.age)
print(laowang.address)
print(laowang.phone)
print(laozhao.phone)
```

```python
# 给对象添加方法
class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def eat(self):
        print("------%s正在吃----"%self.name
def run(self):
    print("------------%s正在跑----"%self.name)
p1 = Person("p1",10)
import types
p1.run = types.MethodType(run,p1)#把run方法的引用和p1这个实体绑定
p1.run()
```

```python
#限制随意添加属性
class Person(object):
    __slots__ = ("name","age")#限制Person类的属性只能由name和age这两个，不能动态新增
```



