1.import 将python功能引入脚本程序，不会给出全部功能，而是在需要什么的时候就调用什么

from sys import argv

argv就是所谓的参数变量

```python
script, first, second, third = argv
```

“把 argv 中的东西解包，将所有的参数依次赋予左边的变量名”

2.if语句：

```python
userName = input("你的用户名:")
if userName == "abcd":
    password = input("你的密码:")
    if password == "1234":
        print("恭喜%s 同学登陆系统" % userName);
elif not userName == "def" or userName == "fgh":
    print("黑客滚蛋");
else:
    print("登陆失败检查用户名和密码")
```

not 表示取反

```python
i = 1
while i <= 9 :
    j = 1
    while j <= i :
        print("%d * %d = %d "%(i, j, i*j), end="\t")
        j += 1;
    print()
    i += 1;
```

