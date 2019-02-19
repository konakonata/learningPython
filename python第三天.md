1.函数

一块具有独立功能的代码块，重复使用。函数把这块代码封装成一个整体

def 函数名(参数):

​	函数代码;

help(函数名) 查看函数文档说明

2.返回值

```python
name = 3
def test(a, b):
    global name; #对全局变量进行再次声明，方便对全局变量的再次赋值，而且再次赋值是作用于全局的
    print("aaa")
    return a + b;
a = test(1, 2)
print(a)
#只是为了增加辨识度，自定义的main函数
def main(): #main函数，但是python没有默认的main函数也是可以执行的，不同于java和c
    do;
    
name = "xiaowang"
def doS():
    global name #不加global 异常：UnboundLocalError: local variable 'name' referenced before assignment
    print(name)
    name = 3
doS()
print(name)#结果是3，
```

3.多个返回值

```python
result = [1, 2, 3] #把结果封装成列表 result[i]取值
result = {key : value , key1: value} #把返回值封装成字典 result[key]取值
result = (1, 2, 3)  #把返回值封装成元组 result[i]取值
#元组取值第二种 a, b = test(); return a, b; 什么都不加，只有两个数据，默认的就是元组格式
```

4.缺省参数：

```python
def test(a, b, c = 2):
    print(a)
    print(b)
    print(c)
test(1, 2)
```

5. 不定长参数

```python
def test(*args) :#不定长元组参数
    print(args)
test(1,2,3,4,5,6) #结果是元组(1, 2, 3, 4, 5, 6)
```

```python
def test(**kwargs) : #不定长键值对参数
    print(kwargs)
test(a = 1, b =2 , c= 3)#结果是字典{'a': 1, 'b': 2, 'c': 3}
```

6.引用传参

python中可变数据类型：列表/字典，多个变量指向同一个地方，改动一个其他的都会相应改动

不可变类型：数字/字符串/元组，多个变量指向同一块内存，改动会开辟新的内存。 

字典中只有不可变的数据类型才能做key

7.传递引用其实就是传递内存空间的地址，

+=会同时改变传入可变类型实参的值， = +会新建内存空间再赋值

8.递归：就是反复的调用自己本身来达成某种类似循环的目的

9.匿名函数

```python
lambda args : pass #匿名函数不能直接调用print，lambda需要表达式
def test (a, b, xxx):
    return xxx(a, b)
result = test(a , b, lambda x, y : x - y)#通过改这里的加减乘除号就能进行不同的运算，匿名函数可以作为参数传递
b = []
b.sort(key = lambda x : x[key1], reverse = true)#用字典中的某个键值来排序，true为倒序
```

10.加特殊字符或者其他标识来区别全局变量

函数名可以相同，但是后者会自动覆盖前者

11.全局变量和局部变量名字相同时，函数是就近原则，优先使用本函数的局部变量



