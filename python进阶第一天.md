linux命令格式：

commond [-options]   参数

\反斜杠，转义符      /根目录

1. ls:显示当前文件夹下的文件

   ls  文件夹名: 可以直接查看指定文件夹下的内容

   ls 通配符查询文件

   ls > test.txt  将当前目录数据放到test.txt中，清空test.txt中的内容，然后把新内容放入，用>>就是在文件末尾追加内容，而不是覆盖

   ls -alh 查询目录全部信息

   ls -l 查看目录部分信息

2. pwd：获取当前绝对路径

3. cd - : 回到上次操作 的路径，cd ~ : 跳转到当前目录的home路径

4. touch：创建文件

5. more 文件名:分屏显示文件内容，或者文件夹下的文件目录，按空格会下一页

6. gedit 文件名 ：窗口打开文件

7. | ：管道 连接两个命令，把第一个命令的结果给管道，然后第二个命令操作管道中的数据

8. clear : 清屏

9. mkdir： 创建目录，也就是创建文件夹

10. tree： 显示当前目录的树状结构

11. mkdir a/b/c -p : 没有a和b就创建a和b文件夹

12. rmdir 文件夹名：删除文件夹，不能删除非空文件夹

13. rm -rf 目录： 强制递归删除所有文件子文件

14. ln ： 建立链接文件，相当于创建快捷方式

    软连接 ： ln -s 源文件 链接文件，删除原文件，软连接就无法打开

    硬连接： ln 原文件 链接文件，如果原文件更改，链接文件也会同步修改，删除原文件，链接文件还会存在

    软连接是直接删除文件内容，硬连接只是删除链接文件内容的文件名，而内容不会删除，只是删除了1个文件内容对应的连接数，所以一个文件所有硬连接公用一个文件内容

15. cat: 以只读的方式查看文件内容

    cat 01.py >> 02.py 追加到

    cat  01.py > a.text 覆盖到

    所有将结果都显示在终端上的，都可以用重定向来进行保存

16. grep： 在文件中查找指定内容

    grep -n "^a" a.txt : 查找文件中是否有包含以a开头的数据

    -n 表示行号， -i 忽略大小写 ， -v 不包含指定内容的数据

    grep “t[xn]t” a.txt: 查找文件中是否包含txt或tnt的内容

    ^a 以a开头，a$ 以a结尾，[ab]包含a或b的内容

17. man 命令名： 查看命令文档

    命令 --help： 查看命令说明

18. history： 查看历史命令

19. find 目录 -name 文件名或者正则表达式，在目录中通过文件名查找文件

    -size 按文件大小查找  +2m 大于2m， -2m小于2m， 2m等于2m

20. 拷贝文件：

    cp 原文件路径 目标路径

    -v 显示拷贝进度， -r把文件夹和其子文件都拷贝，-i 添加提示， -f 强制拷贝

21. 剪切移动重命名文件：

    mv  原文件路径  目标路径

    mv 原文件名 新文件名

22. 压缩和归档：

    打包: tar -cvf  目标包名 待打包目录  （生成*.tar文件）

    解包; tar -xvf 包名 解压到指定目录

    压缩： gzip  tar包 （生成*.tar.gz文件）

    解压缩： gzip -d  压缩包

    打包加压缩：tar -zcvf  *.tar.gz  待打包目录(直接生成x.tar.gz文件)

    解压缩加解包：tar -zxvf *.tar.gz 解压到指定目录

    tar -jxvf xxx.tar.bz2 解压bzip的压缩包

    windows中的zip文件：

    zip [-r]  压缩文件名 待压缩路径

    unzip -d   目标目录 压缩文件名

23. 查找命令的路径：

    which 命令名

24. 用户、权限管理：

    远程登录linux： ssh 用户名@ip

    who查看当前多少人登录系统

    whoami：查看当前用户

    exit：退出登录账户

    1)添加用户账号：useradd或者adduser，adduser是useradd的一个软连接

    sudo useradd  username -m(自动创建家目录) 

    -d(指定家目录)  /home/username

    设置密码:

    sudo passwd username

    切换账户：

    su username

    删除用户：

    userdel username 删除用户，但不会删除用户的主目录

    userdel -r username 删除用户，同时删除用户的主目录

    2)添加组：

    sudo -s 接下来的所有操作都是sudo直接操作，转成了root权限，之后的操作就不用加sudo了

    cat /etc/group 查看组信息

    groupmod  + 2次tab 查看组信息

    创建用户组：groupadd groupname

    删除用户组： groupdel groupname

    更改用户绑定到用户组：usermod -g 用户组 用户名， usermod -a -G 用户组 用户名。-g指定主要组，-G指定次要组

    查找用户所在组：groups 用户名

    一个账户可以属于多个组

    3）为普通用户添加sudo

    sudo usermod -a -G adm 用户名

    sudo usermod -a -G sudo 用户名

25. chmod 修改权限:

    文件信息 d-目录，r-读取， w-写入， - 没权限， x - 执行，

    chmod u/g/o/a +/-/= rwx 文件名(u-user表示文件所有者，g-group，同组人员，o-other组外其他人， a-all  这三者全是， + 添加权限， - 撤销权限， = 设定权限)

    人员中间用逗号

    数字模式（4-可读，2-可写，1-可执行，7-全部权限）第一个数字对应u，第二个数字对应g，第三个对应o

    chmod 777 文件夹 -R （同步修改文件夹内所有文件的权限）

26. cal 显示日历

27. date显示当前时间

28. %y年  %m 月份 %d日

29. ps：查看当前终端运行的进程

    ps -aux 显示所有进程信息

    kill 进程号 -9 强制关掉进程

30. df显示磁盘信息

31. sublime编辑器

32. mount挂在命令

33. vim编辑器

    vi支持三种模式：

    1)命令模式：打开之后的默认模式

    2)编辑模式：按 i/o/a/I/O/A 打开编辑模式, esc返回命令模式

    3)末行模式：冒号进入末行模式，w-保存，wq 保存退出，q-不保存退出，q!-强制退出， X-加密， x-保存退出

    vim 文件名 +行号，直接进入文件并跳转到指定行号

    按r然后输入，将会替换光标处的字符

    /字符  查询文本中的指定字符

    :%s/abc/123/g 将光标所在行abc替换成123’

    :1,10s/dong/Dong/g  将1到10行之间的dong替换成Dong

    :!命令，能直接在文本中执行shell命令

34. ubuntu安装软件

    apt-get install 软件名

    apt-get update 在重新设置下载源之后，让sources.list文件的更改生效

    /etc/apt/sources.list 文件中保存了ubuntu下载软件的源，一般替换成阿里源

    apt-get remove package 删除软件

35. samba服务器的搭建

    安装：sudo apt-get install samba samba-common

    创建共享文件夹： mkdir share（文件夹名，随意）

    修改权限 chmod 777 share

    修改samba配置文件：vi /etc/samba/smb.conf

    在文件莫问添加:

    [share]

    ​	path = 共享文件夹路径

    ​	available = yes

    ​	browseable = yes

    ​	#public = yes #不需要密码

    ​	writable = yes

    创建samba账户：

    ​	touch /etc/samba/文件名， 密码存放地址

    ​	文件名 -a 用户名

    重启samba：

    ​	/etc/init.d/smbd restart

    访问服务器

    ​	smb://服务器ip

    ​	windows用运行输入\\\服务器ip地址



