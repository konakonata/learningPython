1.私有属性

```python
class Person:
    def __init__(self, name, age):
        #在属性名前面有两下划线，表示该属性是私有属性
        #不能用对象.属性来赋值或获取值
        self.__name = name
        self.__age = age
    #私有属性只能通过getter和setter方法来进行赋值和取值
    def setName(self, name):
        self.__name = name

    def getAge(self):
        return self.__age
    
    #私有方法，不会暴露给外部，只能通过类的其他函数来调用此私有方法
    def __test(self):
        print("我的天啊")
        
    def test(self) :
        self.__test()
        print(self.__name)
    
    def __del__(self): #程序结束后，python解析器会自动调动del函数来删除还存在的对象
        print("del")

laowang = Person("老王", 30)

#在外部对私有属性进行赋值，只是针对单个对象，而不是更改属性初始化的值，而且不是调用原有属性，只是给此对象新增了一个__age的属性
laowang.__age = 18


zhangsan=laowang
#对象的删除删除的是对象名到内存地址的硬链接，删除laowang，zhangsan依然存在，并没有删除内存地址
del laowang #手动删除对象

print(zhangsan) #是正确的，不会报错
print(laowang.getAge())#结果是30
print(laowang.__age)#结果是18
print(laowang.test())#如果test()函数没有返回值，打印的最后一个结果是none
laowang.test()#这边不使用print方法来输出，因为函数本身自带print，
```

