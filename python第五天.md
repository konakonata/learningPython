1.类和对象

定义一个类： class 类名: xxxxxxx,   创建 一个对象：变量名  = 类名()，self就是java 的this

```python
class Cat:
	def __init__(self):#这个方法是python自带，当你创建这个对象的时候，这个方法就会被调用，进行初始化操作
		self.color =""
		self.weiba=""
	def __init__(self, var1, var2):#初始化函数也可能传多个参数，就相当于java的构造函数
        print(var1 + var2)
	def eat(self) :
		print("吃吃")
	def printInfo(self): #self就是调用函数的实例
		print(self.color)
#创建一个cat对象
cat = Cat()
cat = Cat(var1, var2)#有参构造器
#也可以在外部动态添加属性，但是要在其他方法调用之前,这就只是改变一个对象的，并不会操作类
cat.color = ""
cat.weiba = ""


#我的操作
class Cat :
    def __init__(self):#init函数左右各是两个下划线
        self.color = "虎斑"
        self.name = "美短"
    def printInfo(self):
        print("这是一只%s%s"%(self.color, self.name))
	def __str__(self):#相当于重写toString方法
        print()

cat = Cat()
cat.printInfo()



```

总结：python没有独立的声明类的属性，一切操作都是在初始化函数中进行，或者在外部进行单个对象的属性赋值 

魔法方法：拥有特殊功能的函数比如__str__()和__init__()

2.练习

```python
class SweetPotato:
    def __init__(self):
        self.cookedLevel = 0
        self.cookedString = "生的"
        self.condiments = []
    #重写str方法
    def __str__(self):
        #python中其他基本数据类型不能自动转成str，必须要强制转型
        return "地瓜的生熟程度为:" + self.cookedString + "等级为：" + str(self.cookedLevel) + "佐料为：" + self.condiments
    #烤地瓜的方法
    def cook(self, times):
        self.cookedLevel += times

        if self.cookedLevel > 3 and self.cookedLevel < 5:
            self.cookedString = "半生不熟"
        elif self.cookedLevel > 5 and self.cookedLevel < 8:
            self.cookedString = "熟了"
        elif self.cookedLevel > 8:
            self.cookedString = "焦了"
        else:
            self.cookedString = "生的"
    #添加佐料
    def addCondiments(self, temp):
        self.condiments.append(temp)
diGua = SweetPotato()
diGua.cook(7)
print(diGua)
```

练习2：

```python
class Home :
    def __init__(self, area):
        self.area = area
        self.containsItem = []
    def __str__(self):
        msg = "家当前可用面积为：" + str(self.area)
        msg += "\n"
        msg += "家里的物品有:"
        for temp in self.containsItem:
            msg += temp.name + ","
        #第二种方式
        #msg += ','.join(self.containsItem)
        msg = msg[:-1]
        return msg

    def addItem(self, item):
        if self.area > item.area:
            self.containsItem.append(item)
            self.area -= item.area
class  Bed :
    def __init__(self, name, area):
        self.name = name
        self.area = area
    def __str__(self):
        msg = self.name + "床的面积为:" + str(self.area)

#创建一个床
bed = Bed("西蒙斯", 1.5)
#创建一个家
home = Home(123)

home.addItem(bed)
home.addItem(Bed("硬板床",120))
#第二种方式
#home.addItem(bed.name)
print(home.__str__())
```

