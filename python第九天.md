1.python中的包

```
import 包名.模块名  文件夹中包含__init__.py文件，就是包
from 包名 import 模块名
```

2.模块的制作安装和使用

1.发布时，在和包同级路径下创建setup.py文件

![1538124721544](C:\Users\konakona\AppData\Roaming\Typora\typora-user-images\1538124721544.png)

2.这个文件必须叫setup.py,   文件内容如下

![1538124766408](C:\Users\konakona\AppData\Roaming\Typora\typora-user-images\1538124766408.png)

3.执行python setup.py build 命令,生成build文件夹

4.生成发布压缩包 python setup.py sdlist  打包后生成发布压缩包

5.安装压缩包

1)解压 tar -zxvf 压缩包名

2)进入解压文件夹

3)python3 setup.py install 执行命令 安装，之后就可以导入使用（直接用python是用python2进行安装，python3才是python3）



