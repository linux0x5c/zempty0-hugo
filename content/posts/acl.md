---
title: "Acl"
date: 2019-01-07T15:25:12+08:00
draft: false
---

##Linux之用户管理与权限控制（下）

**2.组管理命令**

**a、groupadd**

groupadd命令用来创建组。

```bash
groupadd - create a new group
synopsis: groupadd [options] group

options:
    -g : 指定GID号
    -r : 指定为一个系统组
```

**实例：**

```
[root@localhost ~]# groupadd -g 700 -r centos
```

**b.groupmod与groupdel**

groupmod命令用来修改组的属性，groupdel命令用来删除组。

```
groupdel - delete a group
synopsis: groupdel GROUP_NAME

groupmod - modify a group definition on the system
synopsis: groupmod [options] GROUP
options:
    -g : 修改GID号码
    -n : 修改组名称 （groupmod -n NEW_GROUP OLD_GROUP）
```

**实例：**

```
[root@localhost ~]# groupmod -g 701 -n redhat centos #修改属性
[root@localhost ~]# getent group redhat
```

```
[root@localhost ~]# groupdel redhat #删除rehat组
[root@localhost ~]# getent group redhat
[root@localhost ~]# 
```

注：当有用户将该用户组作为主组时，无法直接删除该用户组，必须先删除用户。

**c、gpasswd**

gpasswd命令用来修改组密码。

```shell
gpasswd [OPTION] GROUP 设置组密码
-a user: 将user添加至指定组中；
-d user: 从指定组中移除用户user
-A user1,user2,…: 设置有管理权限的用户列表
```

**实例：**
​	
**将用户添加至组中，并设置管理员：**

```bash
[root@localhost ~]# groupadd admins
[root@localhost ~]# gpasswd -a user1 admins
Adding user user1 to group admins
[root@localhost ~]# gpasswd -A user1 admins
[root@localhost ~]# getent group admins
admins:x:1011:user1
[root@localhost ~]# gpasswd -a user2 admins
Adding user user2 to group admins
[root@localhost ~]# getent group admins    
admins:x:1011:user1,user2
[root@localhost ~]# getent gshadow admins
admins:!:user1:user1,user2
```

**设置组密码：**

```
[root@localhost ~]# gpasswd admins
Changing the password for group admins
New Password: 
Re-enter new password: 
```

**d.newgrp**

netgrp命令用来临时切换基本组。

```
newgrp - log in to a new group
synopsis: newgrp [-] [group]
    - : 模拟用户登陆， 以实现重新初始化环境变量
```

**实例：**
​	

```
[root@localhost ~]# su - user3
[user3@localhost ~]$ id
uid=1009(user3) gid=1010(user3) groups=1010(user3)
[user3@localhost ~]$ newgrp - admins
Password: 
[user3@localhost ~]$ id
uid=1009(user3) gid=1011(admins) groups=1011(admins),1010(user3)
[user3@localhost ~]$ 
```

**e、groupmems与groups**

更改和查看组成员。

```
groupmems [options] [action]
options：
-g：更改为指定组(只有root)
-a：指定用户加入组
-d：从组中删除用户
-p：从组中清除所有成员
-l：显示组成员列表
```

**实例一：**

```shell
[root@localhost ~]# groupmems -a user3 -g admins
[root@localhost ~]# id user3
uid=1009(user3) gid=1010(user3) groups=1010(user3),1011(admins)
[root@localhost ~]# groupmems -l -g admins
user1  user2  user3 
[root@localhost ~]# groupmems -p -g admins
[root@localhost ~]# getent group admins
admins:x:1011:
```

**实例二：**

```
[root@localhost ~]# groups user1 #查看用户user1的组信息
user1 : user1 root bin
[root@localhost ~]# 
```

**3.管理文件权限**

![](http://p1.bpimg.com/4851/884a5f12c5de0fc8.png)

**a、文件属性操作**

chown 设置文件的所有者

```
chown[OPTION]… [OWNER][:[GROUP]] FILE… 设置文件的所有者和所有组
选项：
-R: 递归修改
–reference=FILE1 FILE2… 以file1做参考文件修改file2的属主属组
OWNER  只修改属主
OWNER:GROUP  同时修改属主属组
:GROUP  只修改属主
命令中的冒号可用.替换；

用法：
OWNER
OWNER:GROUP
:GROUP
```

**实例：**

```shell
[root@localhost cwn]# ll
total 0
-rw-r--r-- 1 root root 0 Jan  1 06:30 test.txt
[root@localhost cwn]# chown user1 test.txt 
[root@localhost cwn]# ll test.txt 
-rw-r--r-- 1 user1 root 0 Jan  1 06:30 test.txt
[root@localhost cwn]# chown :user2 test.txt 
[root@localhost cwn]# ll test.txt 
-rw-r--r-- 1 user1 user2 0 Jan  1 06:30 test.txt
[root@localhost cwn]# ll -d .
drwxr-xr-x 2 root root 4096 Jan  1 06:30 .
[root@localhost cwn]# chown -R root.root /root/cwn/
[root@localhost cwn]# ll test.txt 
-rw-r--r-- 1 root root 0 Jan  1 06:30 test.txt
```

chgrp 设置文件的属组信息

```
chgrp[OPTION]… GROUP FILE… 修改文件的属组
-R 递归
–reference=FILE1 FILE2… 以file1做参考文件修改file2的属组
```

**实例：**

```shell
[root@localhost cwn]# ll test.txt 
-rw-r--r-- 1 root root 0 Jan  1 06:30 test.txt
[root@localhost cwn]# chgrp user1 test.txt 
[root@localhost cwn]# ll test.txt 
-rw-r--r-- 1 root user1 0 Jan  1 06:30 test.txt
[root@localhost cwn]# ll -d .
drwxr-xr-x 2 root root 4096 Jan  1 06:30 .
[root@localhost cwn]# chgrp -R user3 /root/cwn
[root@localhost cwn]# ll -d .
drwxr-xr-x 2 root user3 4096 Jan  1 06:30 .
[root@localhost cwn]# ll test.txt 
-rw-r--r-- 1 root user3 0 Jan  1 06:30 test.txt
```

**4.文件权限说明**

文件的权限主要针对三类对象进行定义：
​	

```
owner: 属主, u
group: 属组, g
other: 其他, o
```

每个文件针对每类访问者都定义了三种权限：

```
r: Readable
w: Writable
x: eXcutable
```

文件：

```
r: 可使用文件查看类工具获取其内容
w: 可修改其内容
x: 可以把此文件提请内核启动为一个进程
```

目录：

```
r: 可以使用ls查看此目录中文件列表
w: 可在此目录中创建文件，也可删除此目录中的文件
x: 可以使用ls -l查看此目录中文件列表，可以cd进入此目录
X:只给目录x权限，不给文件x权限
```

**八进制数字表示权限：**

```
 — 000 0
–x 001 1
-w- 010 2
-wx 011 3
r– 100 4
r-x 101 5
rw- 110 6
rwx 111 7
例如：
640: rw-r—–
rwxr-xr-x: 755
```

**文件权限操作命令chomod：**

```
chmod[OPTION]… OCTAL-MODE FILE…
chmod[OPTION]… MODE[,MODE]… FILE…
-R: 递归修改权限
–reference=RFILE FILE… 以file1做参考文件修改file2的权限
MODE：
修改一类用户的所有权限：
u= g= o= ug= a= u=,g=
修改一类用户某位或某些位权限
u+ u- g+ g- o+ o- a+ a-
例如：
chmod u+wx,g-r,o=rx file
chmod -R g+rwX /testdir
chmod 600 file
chmod o= file #取消其他用户对此文件的所有权限
```

**新建文件和目录的默认权限**

umask值可以用来保留在创建文件权限

新建FILE权限: 666 -umask

新建DIR权限: 777 -umask

普通用户的umask是002

root用户的umask是022

**umask命令：**

```
umask: 查看
umask #: 设定（umask 002） =====>临时设置umask
umask –S 模式方式显示
```

修改全局设置：/etc/bashrc 用户设置：~/.bashrc永久修改umask

实例：

```
[root@localhost ~]# umask
0022
[root@localhost ~]# umask -S
u=rwx,g=rx,o=rx
[root@localhost ~]# umask 777
[root@localhost ~]# umask
0777
```

**5、文件与目录的三种特殊权限及ACL**

三种特殊权限SUID、SGID、SBIT

**SetUID**: 

```
1.SUID权限仅对二进制程序有效；

2.执行者对改程序需要具有x的可执行权限；

3.本权限尽在执行该程序的过程中有效

4.执行者将具有改程序所有者的权限
```

权限设定：

```
chmod u+s FILE...
chmod u-s FILE...
```

eg: 1. user1为普通用户对于/usr/bin/passwd这个程序来说是具有x的权限的，表示user1能执行passwd;

```
1.passwd的拥有者是root这个账号；

2./etc/shadow可以被user1所执行的passwd所修改； 注：SUID仅可用在二进制程序上，不能够用在shell script上面。所以SUID的权限部分，还是要看shell script调用进来的程序设置，而不是shell script本身。 
```

![](http://i.imgur.com/zVLTk7N.png)

**SetGID** 
​	

```
1.SGID对二进制程序来说需具备x的权限

2.SGID对二进制程序有用

3.执行者在执行的过程中将会获得改程序用户组的支持
```

![](http://i.imgur.com/gy0G2Wv.png)

除了二进制文件之外，事实上SGID也能够用在目录上，这也是常用的一种用途。当一个目录设置了SGID的权限后，他将具有如下功能： 

```
1.用户若对于此目录有r与x的权限时，该用户能进入该目录；

2.用户在此目录下的有效用户组将会变成该目录的有效用户组（有效用户组即是主组，不同叫法而已）；

3.若用户在此目录下具有w权限（可以新建文件），则用户所创建的新文件的用户组与此目录的用户组相同。 
```

权限设定：

```
chmod g+s FILE|DIR...
chmod g-s FILE|DIR...

```

![](http://i.imgur.com/sA8Zq8k.png)

**Sticky Bit** （SBIT）目前只针对目录有效，对文件已经没有效果了。 
​	

```
1.当用户对于此目录有w、x权限，即具有写入权限；

2.当用户在该目录下创建文件或目录时，仅自己与root才有权利删除该文件。

3.如果将A目录加上SBIT的权限项目时，则甲只能针对自己创建的文件或者目录进行删除、改名、移动等操作，而无法删除他人的文件。

```

权限设定：
​	

```
chmod o+t DIR...
chmod o-t DIR...

```

![](http://i.imgur.com/wnQmTit.png)

**SUID/SGID/SBIT权限设置**

```
4为SUID

2为SGID

1为SBIT

```

使用命令chmod为文件或者目录增加三种特殊权限，分别对应chmod u+s 文件名或者目录,chmod g+s 文件名或者目录,chmod o+t 文件名或者目录。

**权限位映射：**

```
SUID: user,占据属主的执行权限位
	s: 属主拥有x权限
	S：属主没有x权限
SGID: group,占据属组的执行权限位
	s: group拥有x权限
	S： group没有x权限
Sticky: other,占据other的执行权限位
	t: other拥有x权限
	T： other没有x权限

```



**三种特殊权限的应用场景和作用：**

**Suid的应用场景和作用：**Suid只对二进制程序起作用，程序发起者在程序执行的时候继承程序拥有者的身份以程序拥有者的权限，以程序拥有者的身份去执行程序。应用场景为root用户给普通用户分配需要经常改动的配置文件的修改权限比如密码passwd.

**Sgid作用：**对二进制程序的作用与Suid基本一致。对目录的作用，当目录设置Sgid时，若用户在此目录下所创建的新文件的用户组与此目录的用户组一致。应用场景：在开发的时候需要将开发的东西共享出来大家都可以随时加入新成员不用再设置所属组对共享文件进行查看修改。

**Sticky:**目录设置后，所有用户只能对自己创建的文件进行增删改查，无法对其他人的文件进行删除、移动、重命名等操作。

**特殊权限数字法**

```
SUID SGID STICKY
000 0
001 1
010 2
011 3
100 4
101 5
110 6
111 7
~]#chmod 4777 /tmp/a.txt

```

**6、主机的具体权限规划：ACL的使用**

**ACL:**ACL是Access Control List的缩写，主要目的是提供传统的owner、group、others的read、write、execute权限之外的权限设置。ACL可以针对单一用户、单一文件或者目录进行r、w、x的权限设置，对于需要特殊权限的使用状况非常有帮助。

```
CentOS7.0默认创建的xfs和ext4文件系统有ACL功能。
CentOS7.X之前版本，默认手工创建的ext4文件系统无ACL
功能。需手动增加:
tune2fs –o acl /dev/sdb1
mount –o acl /dev/sdb1 /mnt
ACL生效顺序：所有者，自定义用户，自定义组，其他人

```

**查看方法：**

```
[root@localhost ~]# dumpe2fs -h /dev/sda1 | grep 'Default mount' 
dumpe2fs 1.41.12 (17-May-2010)
Default mount options:    user_xattr acl

```

或者：

```
[root@localhost ~]# tune2fs -l /dev/sda1 | grep option
Default mount options:    user_xattr acl

```

**mask说明：**

权限掩码。用来控制你所设置的扩展ACL权限。你所设置的权限必须存于mask规定的范围内才会生效。也就是所谓的“有效权限（effective permission）”

**设置mask实例：**

```
[root@localhost ~]# ll test 
-rw-rwxr--+ 1 root root 0 Jan  1 21:05 test
[root@localhost ~]# getfacl -e test 
# file: test
# owner: root
# group: root
user::rw-
user:user1:rwx                  #effective:rwx
group::r--                      #effective:r--
mask::rwx
other::r--

[root@localhost ~]# setfacl -m m:r test 
[root@localhost ~]# getfacl -e test 
# file: test
# owner: root
# group: root
user::rw-
user:user1:rwx                  #effective:r--
group::r--                      #effective:r--
mask::r--
other::r--

```

注：用户rwx的ACL设置信息。最终权限由mask控制，你所设置的权限必须在mask内，否则相对mask多出来的权限也是无效的。

**主要针对几个项目控制权限**：

```
1.用户：可以针对用户来设置权限

2.用户组：可以对用户组来设置权限

3.默认属性：还可以在该目录下在新建文件/目录时设置新数据的默认权限

```

**ACL设置技巧**：getfacl、setfacl

```
1.getfacl:取得某个文件或者目录的ACL设置项目；

2.setfacl：设置某个目录或者文件的ACL规定；

```

**setfacl命令用法**：setfacl [-bkRd][{-m|-x} acl参数] 目录文件名

```
-m, --modify=acl：修改文件或目录的扩展ACL设置信息。

-M, --modify-file=file：从一个文件读入ACL设置信息并以此为模版修改当前文件或目录的扩展ACL设置信息。

-x, --remove=acl：从文件或目录删除一个扩展的ACL设置信息。

-X, --remove-file=file：从一个文件读入ACL设置信息并以此为模版删除当前文件或目录的ACL设置信息。

-b, --remove-all：删除所有的扩展的ACL设置信息。

-k, --remove-default：删除缺省的acl设置信息。
--set=acl：设置当前文件的ACL设置信息信息。
--set-file=file：从文件读入ACL设置信息来设置当前文件或目录的ACL设置信息。
--mask：重新计算有效权限，即使ACL mask被明确指定。

-n, --no-mask：不要重新计算有效权限。setfacl默认会重新计算ACL mask，除非mask被明确的制定。

-d, --default：设置默认的ACL设置信息（只对目录有效）。

-R, --recursive：操作递归到所有子目录和文件。

-L, --logical：跟踪符号链接，默认情况下只跟踪符号链接文件，跳过符号链接目录。

-P, --physical：跳过所有符号链接，包括符号链接文件。
--restore=file：从文件恢复备份的acl设置信息（这些文件可由getfacl -R产生<---针对目录）。通过这种机制可以恢复整个目录树的acl设置信息。此参数不能和除--test以外的任何参数一同执行。
--test：测试模式，不会改变ACL设置信息。

-v, --version：显示程序的版信息。

-h, --help：显示帮助信息。

```

**getfacl命令用法**

```
getfacl [-aceEsRLPtpndvh] file...

-a , --access：显示文件或目录的访问控制列表。

-d , --default：显示文件或目录的默认（缺省）的访问控制列表。

-c , --omit-header：不显示默认的访问控制列表。

-R , --recursive：操作递归到子目录。

-t , --tabular：使用列表输格式出ACL设置信息。

-n , --numeric：显示ACL信息中的用户和组的UID和GID。

-p , --absolute-names：

-v , --version：显示命令的版信息                                                

-h , --help：显示命令帮助信息。

```

**设置acl实例**：

```
 getfacl file |directory   #获取acl信息
 setfacl -m u:young:rwx file|directory
 setfacl -Rm g:sales:rwX directory #递归设置directory下所有文件acl
 setfacl -m g:salesgroup:rw file| directory
 setfacl -m d:u:young:rx directory
 setfacl -x u:wang file |directory #删除制定的acl
 getfacl -e file | directory   #获取有效权限（effective permission）
 setfacl -b file | directory #删除全部的acl
 setfacl -d -m u:young:rw directory ##这个目录下的所有文件和目录继承dirctory的acl权限
 getfacl file1 | setfacl --set-file=- file2 #设置file2与file1相同的acl

```

注：acl设置成功后文件属性的权限最后一位有一个“+”号

```
[root@localhost ~]# ll host 
-rw-rw----+ 1 root root 59 Jan  1 20:48 host

```

**备份和恢复ACL设置信息**

```
[root@localhost testdir]# getfacl -R ./dir/ > acl.txt
[root@localhost testdir]# cat acl.txt
# file: dir/
# owner: root
# group: root
# flags: -s-
user::rwx
group::r-x
group:g1:rw-
mask::rwx
other::r-x
[root@localhost testdir]# setfacl -b -R ./dir/
[root@localhost testdir]# ll -d .
drwxr-xr-x. 3 root root 4096 Aug  4 22:16 .
[root@localhost testdir]# ll  -d ./dir
drwxr-sr-x. 2 root root 4096 Aug  4 22:07 ./dir
[root@localhost testdir]# setfacl --restore=acl.txt
[root@localhost testdir]# ll  -d ./dir
drwxrwsr-x+ 2 root root 4096 Aug  4 22:07 ./dir

```

**补充命令：**

设置文件特定属性与查看文件特殊属性命令chattr与lsattr

```
chattr +i 不能删除，改名，更改
chattr +a 只能增加
charttr -i  | charttr -a去除
lsattr 显示特定属性

```

**实例：**

```
[root@localhost ~]# chattr +i host 
[root@localhost ~]# rm -fr host
rm: cannot remove `host': Operation not permitted
[root@localhost ~]# lsattr host 
----i--------e- host
[root@localhost ~]# chattr -i host
[root@localhost ~]# lsattr host 
-------------e- host

```

（完）