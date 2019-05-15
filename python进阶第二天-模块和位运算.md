1. import导入模块

   import 搜索路径

   ```python
   import sys
   sys.path
   sys.path.append('/home/zfc')#绝对路径
   ```

2. 重新导入模块

   在程序没有结束前，对导入的模块进行修改，模块。程序中并不会改变，如果要改变就要重新导入

   ```python
   from imp import *
   reload(test) #重新导入模块
   ```

3. 循环导入

   两部分代码互相被当作模块导入，不能进行这种操作，会进入死循环，我导入了我自己？！

4. == 和is的区别

   ==只是判断左右两边值是否相等

   is是判断左右两边是否指向同一个内存地址

   id(变量)查看变量的内存地址

   如果两个变量的值在-126到125之间的整数，值相同，那地址也相同

5. ```python
   #浅拷贝，
   a=[11,22,33]
   b=a
   #a和b内存地址相同，a改变b也会改变
   #深拷贝：
   import copy
   a = [11,22,33]
   c = copy.deepcopy(a)
   #此时c和a的内存地址不同，c新开辟内存空间，并将a的数据复制过来
   ```

6. copy和deepcopy的不同

   ```python
   import copy
   a = [11,22,33]
   b = [44,55,66]
   c = [a,b]
   d = c #浅拷贝
   e = copy.deepcopy(c)#深拷贝
   #那么此时e中的是a和b的引用，还是a和b的深拷贝
   a.append(44)
   c[0] #结果[11,22,33,44]
   e[0] #结果[11,22,33] 说明e中是a和b的深拷贝
   e = copy.copy(c)#e和c的地址不同，但是其中的a和b都是同一个引用
   
   c = (a,b) #元组
   e = copy.copy(c)#浅拷贝，e和c的地址相同
   
   ```

7. 进制和位运算

   原码，反码，补码

   正数：原码=反码&补码

   负数：反码=符号位不变+其他位取反

   ​	 补码 = 反码 + 1

   ​	  原码 = 补码符号位不变，数据位取反 ，尾加1

   数据运算要用两个数的补码相加

   bin(a) :把a转成二进制

   hex(a) : 把a转成十六进制

   oct(a): 把a转成8进制

   int(b: 2,8,16) : 把b转成十进制

8. 位运算

   把变量按照byte来运算

   a往左移动一位的结果是a*2

   a往右移动一位的结果是a/2

   右移一位 a >> 1

   左移一位 a << 1

   &按位与，两个二进制数，相对应的位如果都是1才是1，否则就是0

   |按位或，有一个1，结果就是1

   ^按位异或， 对应位不相同，才为1

   ~取反

9. 私有化

   xx : 公有变量

   _xx：单前置下划线，私有化属性或方法，from somemodule import * 禁止导入，类对象和子类可以访问

   __xx：双前置下划线，避免与子类中的属性命名冲突，无法在外部直接访问（名字重整所以访问不到）

   __xx__: 前后双下划线，用户名字空间的魔法对象或属性：例如__init__(), __不要自己发明这样的名字

   xx_:单后置下划线，用于避免与Python关键词冲突

   ```python
   #coding=utf-8 
   #编码格式
   num = 100
   _num1 = 200
   __num2 = 300
   
   from Test import * #无法使用_num1,__num2这两个变量
   
   import Test #可以使用_num1,__num2 ,Test._num1这种方式
   
   #__num2被python变成了_类名__num2
   #实际开发第一种设置getter和setter方法
   class Test(object):
       def __init__(self):
           self.__num = 100
       def setNum(self, newNum):
           self.__num = newNum
       def getNum(self):
           return self.__num
   #第二种通过property设置getter和setter方法
   	num = property(getNum, setNum)
   
   t = Test()
   t.num = 50 #在给num赋值的时候就会调用setter方法，
   t.num #就会调用getter方法
   #第二种property使用方式:装饰器也就是注解
   class Money(object):
       def __init__(self):
           self.__num = 500
       @property
      	def money(self):
           return self.__num;
       
       @money.setter
       def money(self, newNum):
           self.__num = newNum
           
    #赋值
   m = Money()
   m.money = 200 #直接赋值
   print(m.money)#直接取值
      
   ```


