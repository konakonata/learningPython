1.字符串：

type(str) 获取变量的类型

2.for循环

```python
for 临时变量 in 引用:
    do;
else:
    do;
```

字符串下标从0开始：

```python
name = 'abcdf'
pring(name[0])
```

3.切片：字符串引用[起始：结束：步长]，取到结束之前的下标

len(str) 返回字符串的长度

[0:]或[:]或[::]取整个字符串

[::-1]把字符串倒序

[-1]取最后一个字符

[0:-1]取len-1长度的字符串

4.字符串常见操作：

```python
aStr = "hello world"
aStr.find("o")#返回第一个要找的字符的索引位置
aStr.index("o")#同上，但是这个找不到会报错
aStr.rfind("o")#从右找第一个
aStr[aStr.find("o"):]#截取字符串
aStr.count("o")#查找指定字符的个数
aStr.replace("o","O")#被替换和替换内容，只是中间产物，不会改变原来变量的值
aStr.ljust(int), astr.rjust(int), aStr.center(int)#分别是靠左，靠右和居中对齐
aStr.strip(""),aStr.lstrip(" "),aStr.rstrip(" ")#去除两边空格，去左空格，去右空格
aStr.partition(str) #以这个字符切割，但是这个字符不会被删除，返回结果是元组
aStr.splie(str)#以字符分割，但是此字符不会存在于返回结果中，返回结果是列表
aStr.splitlines()#字符串按行分割
aStr.isalpha()#判断是否是纯英文
aStr.isdigit()#判断是否是纯数字
aStr.isalnum()#判断是否是数字和字母的组合
aStr.isspace()#判断是否是纯空格
aStr.join("def")#在str的(len-1)每个字符后面插入aStr，构造出一个新的字符串,结果dabceabcf
mystr = "w o s t c"
name = "sba"
print(mystr.join(name))#sw o s t cbw o s t ca 结果
```

5.列表循环

```python
name_list = ["xiao", "wang", "ba"]
for name in name_list:
    print(name)
i = 0
while i < len(name_list):
    print(name_list[i])
    i += 1
```

6.列表相关操作

```python
name_list = ["xiao", "wang", "ba"]
name_list.append(str)#把字符串追加到列表
name_list.insert(int index, str)#把字符串插入到指定位置
name_list.extend(list) #把列表的所有元素追加到name_list,apeend则是把列表追加到name_list
name_list[1]=""#指定赋值就是修改
str （not in）in name_list#返回boolean值，判断字符串是否在列表中
name_list.count(str)#返回字符串在列表中的数量
name_list.index(str)#返回字符串在列表中的索引
del name_list[2] #删除指定位置元素
name_list.pop() #删除列表最后一个元素
name_list.remove(str)#删除指定值元素
name_list.sort()#正序排序
name_list.sort(reverse = true) #倒序排序
```

7.元组 和列表类似，但元组的元素不能修改

```python
name = ("ab", 123, 3.14)#元组，元组如果只有一个值，后面要加逗号
```

8.字典：dict = {key : value, key1 : value1}

key不能重复，重复会覆盖

取值：value = dict[key]， value = dict.get(key)(这个不会异常)

删除：del dict[key]，dict.clear()(把字典全部清空 , 可用于初始化)

```python
dict.keys()#取得所有key
dict.values()#取得所有value
dict.items()#取得所有键值对，取出是元组组成的列表
dict.has_key(key)#判断是否key存在
for i,chr in enumerate(list) : #遍历列表
    print i,chr; #i代表索引，chr代表列表i索引对应的值
```

