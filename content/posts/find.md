---
title: "Find"
date: 2019-01-06T21:52:23+08:00
draft: false
slug: hugo-integrated-gitalk-plugin
gitalk: true
category: "hugo"
---

#文件查找之locate与find

##locate

locate 让使用者可以很快速的搜寻档案系统内是否有指定的档案。其方法是先建立一个包括系统内所有档案名称及路径的数据库，之后当寻找时就只需查询这个数据库，而不必实际深入档案系统之中了。在一般的 distribution 之中，数据库的建立都被放在 crontab 中自动执行。

**1．命令格式**：

	Locate [选择参数] [样式]

**2．命令功能：**

locate命令可以在搜寻数据库时快速找到档案，locate为模糊查找，数据库由updatedb程序来更新，updatedb是由cron daemon周期性建立的，locate命令在搜寻数据库时比由整个由硬盘资料来搜寻资料来得快，但较差劲的是locate所找到的档案若是最近才建立或 刚更名的，可能会找不到，在内定值中，updatedb每天会跑一次，可以由修改crontab来更新设定值。(etc/crontab)

locate指定用在搜寻符合条件的档案，它会去储存档案与目录名称的数据库内，locate查询文件时，会去搜索/var/lib/mlocate/mlocage.db,寻找合乎范本样式条件的档案或目录录，可以使用特殊字元（如”\*” 或”?”等）来指定范本样式，如指定范本为kcpa\*ner, locate会找出所有起始字串为kcpa且结尾为ner的档案或目录，如名称为kcpartner若目录录名称为kcpa_ner则会列出该目录下包括 子目录在内的所有档案。

locate指令和find找寻档案的功能类似，但locate是透过update程序将硬盘中的所有档案和目录资料先建立一个索引数据库，在 执行loacte时直接找该索引，查询速度会较快，索引数据库一般是由操作系统管理，但也可以直接下达update强迫系统立即修改索引数据库。

**3．命令参数：**

	-e   将排除在寻找的范围之外。
	
	-1  如果 是 1．则启动安全模式。在安全模式下，使用者不会看到权限无法看到	的档案。这会始速度减慢，因为 locate 必须至实际的档案系统中取得档案的权限资料。
	
	-f   将特定的档案系统排除在外，例如我们没有到理要把 proc 档案系统中的档案	放在资料库中。
	
	-q  安静模式，不会显示任何错误讯息。
	
	-n 至多显示 n个输出。
	
	-r 使用正规运算式 做寻找的条件。
	
	-o 指定资料库存的名称。
	
	-d 指定资料库的路径
	
	-h 显示辅助讯息
	
	-V 显示程式的版本讯息

**4.使用实例：**

**实例1：搜索etc目录下所有以sh开头的文件**

	[root@centos7 ~#]locate /etc/sh       
	/etc/shadow
	/etc/shadow-
	/etc/shells
	[root@centos7 ~#]locate -r "/etc/\<sh"  # 正则，锚定词首
	/etc/shadow
	/etc/shadow-
	/etc/shells
	[root@centos7 ~#]

**实例2：忽略大小写**

	[root@centos7 ~#]locate -i ~/d
	/root/Desktop/root/Documents/root/Downloads
	/root/d1
	/root/dd
	/var/lib/pcp/pmdas/root/domain.h
	[root@centos7 ~#]

**实例3：更新数据库**

	[root@centos7 ~#]locate ~/a
	/root/anaconda-ks.cfg
	[root@centos7 ~#]updatedb
	[root@centos7 ~#]locate ~/a
	/root/a.sh
	/root/anaconda-ks.cfg
	[root@centos7 ~#]

##find

**1.主要用途**

find命令是一个实时查找工具，通过遍历指定路径而完成对文件的查找；在使用该命令时，如果不选定参数，则在当前目录下查找子目录与文件并显示之；另外，任何位于参数之前的字符串，都将视为欲查找的目录名。由于是实时遍历查找，find有如下特性：精确实时查找，速度慢可能只搜索用户具备读取和执行权限的目录。

**2.find语法：**

	find [OPTION]... [查找路径] [查找条件] [处理动作]

查找路径：指定具体目标路径，默认为当前目录

查找条件：指定的查找标准，可以是文件名、大小、类型、权限等标准进行；默认为找出指定路径下的所有文件

处理动作：对符合条件的文件做操作，默认输出至屏幕

**3.查找条件**

	1. 根据文件名和inode查找
	2. 根据属主、属组查找
	3. 根据文件类型查找
	4. 根据逻辑组合条件查找
	5. 根据文件大小来查找
	6. 根据时间戳来查找 
	7. 根据权限来查找

**4.处理动作**

	1. -print: 默认动作，显示至屏幕
	2. -ls: 类似于对查找到的文件执行 ls -l 命令
	3. -delete: 删除查找到的文件
	4. -fls file: 查找到的所有长格式的信息保存至指定文件中
	5. -ok COMMMAND {} \;   对查找到的每个文件执行由COMMAND指定的命令，且都会交互式要求用户确认
	6. -exec COMMAND {} \;  对查找到的每个文件执行由COMMAND指定的命令；
	7. {}: 用于引用查找至的文件名称自身
	8. find 传递查找到的文件至后面指定的命令时，查找到所有符号条件的文件一次性传递给后面的命令
	9. 有些命令不能接受过多的参数，此时命令执行可能会失败，用 xargs 来规避此问题
	    find |xargs COMMAND

**5.常用参数**

**文件名和inode类：**

	    -name "文件名称": 支持使用glob, *, ?, [], [^]
	
	    -iname "文件名称": 不区分字母大小写
	
	    -inum n: 按inode号查找
	
	    -somefile name: 相同的inode号文件
	
	    -links n: 链接数为n的文件
	
	    -regex "PATTERN": 以PATTERN匹配整个文件路径字符串，而不仅仅是文件名称

**属主属组类：**

	    -user USERNAME: 查找属主为指定用户(UID)的文件
	
	    -group GROUPNAME: 查找属组为指定组(GID)的文件
	
	    -uid UserID: 查找属主为指定的UID号的文件
	
	    -gid GroupID: 查找属组为指定的GID号的文件
	
	    -nouser: 查找没有属主的文件
	
	    -nogroup: 查找没有属组的文件

**文件类型类：**

	b      block (buffered) special
	
	c      character (unbuffered) special
	
	d      directory
	
	p      named pipe (FIFO)f      regular file
	
	l      symbolic  link
	
	s      socket

**逻辑组合条件类：**

组合条件：

    与：-a
    或：-o
    非：-not, !
    
摩根定律：

    (非P) 或（非Q) = 非(P且Q)
    (非P) 且 (非Q) = 非(P或Q)

![](http://img1.tuicool.com/JRJFbyQ.png!web)
![](http://img0.tuicool.com/rYfmuqM.png!web)

示例：

    !A -o !B = !(A -a B)
    !A -a !B = !(A -o B)

**文件大小类：**

	-size [+|-]#UNIT
	    常用单位：k,M,G 
	#UNIT: (#-1,#]
	    如：5M 表示 (4M,5M]
	-#UNIT: [0,#-1]
	    如：-5M 表示 [0,5M]
	+#UNIT: (#,oo)
	    如：+5M 表示 (6M,oo)

关于文件大小类的解释：为什么-size 5M 还是找精确的5M而是表示(4M,5M], 试想文件的大小指什么？是指文件数据的大小还是包括了元数据后的大小，那你找元数据的大小有意义吗？但文件的大小肯定是包含元数据大小的，而我们一般以文件大小找文件时往往考虑的是文件数据的大小；另外，精确查找一定大小的文件意义不大；所以这里的大小会有1个单位的浮动。

**时间戳类：**

	以”天”为单位：
	    -atime [+|-]#        
	        #: [#,#+1)
	        +#: [#+1,oo)        
	        -#: [0,#)
	    -mtime    
	    -ctime
	
	以“分钟”为单位：
	    -amin    
	    -mmin    
	    -cmin

![](http://s5.51cto.com/wyfs02/M00/86/07/wKioL1ey0_jg-QV8AAAmi8euCLU593.png)

关于时间戳类的解释：为什么-atime 3 表示的是 [3,4),这个就很好解释了，我们这儿所说的时间是指时间段而非时刻，一“天”与一“分钟”都是指一个时间段，只有[3,4)这个半闭半开的区间才能完整地表示第三天。

**权限类：**

	-perm [/|-]MODE
	    MODE: 精确匹配权限
	    /MODE: 任何一类(u,g,o)对象的权限中只要能一位匹配即可，属于或关系。以前用'+',CentOS 7以'/'替代之
	    -MODE: 每一类对象都必须同时拥有指定权限，属于与关系 
	    0：表示不关注 
    
**示例：**

    find -perm 644 表示要严格匹配644的文件
    find -perm +222 表示u,g,o任何一类用户有写权限即匹配
    find -perm -222 表示仅严格匹配写权限，即每个用户必须要有写权限
    find -perm -002 表示仅严格匹配other用户的写权限

**6.使用示例**

**实例1：将配置文件备份到指定目录下并添加扩展名.org**

	[root@localhost ~]# find . -name "*.conf" -exec cp -r {} /testdir/{}.org \; 
	[root@localhost ~]# cd /testdir/
	[root@localhost testdir]# ls
	a.conf.org  b.conf.org
	[root@localhost testdir]# 

**实例2：.提示删除存在时间超过3天以上的属主为young的临时文件**

	[root@localhost ~]# find /tmp -ctime +3 -user young -exec rm -fr {} \;
	[root@localhost ~]# 

**实例3：在主目录中查找可被其它用户写入的文件**

	[root@localhost ~]# find ~ -perm -002
	/root/num
	[root@localhost ~]# find ~ -perm -002 -exec chmod o-w {} \;
	[root@localhost ~]# ll num
	--w--w---- 1 root root 35 Jan 21 05:55 num

**实例4：查找/var目录下属主为root，且属组为mail的所有文件**

		[root@localhost ~]# find /var -user root  -group mail -ls #默认关系就是与
	1179652    4 drwxrwxr-x   2 root     mail         4096 Jan 23 11:04 /var/spool/mail

**实例5：查找/var目录下不属于root、lp、gdm的所有文件**

	[root@localhost ~]# find /var ! -user root ! -user lp ! -user gdm

**实例6：查找/var目录下最近一周内其内容修改过，同时属主不为root，也不是postfix的文件**

		[root@localhost ~]# find /var/ -mtime -7 ! -user root ! -user postfix -ls
	1179676    4 drwx------   3 daemon   daemon       4096 Jan 23 11:04 /var/spool/at
	524399    4 drwx------   2 nginx    nginx        4096 Jan 23 03:16 /var/log/nginx
	524413    0 -rw-r--r--   1 nginx    root            0 Jan 23 03:16 /var/log/nginx/access.log
	524391    0 -rw-r--r--   1 nginx    root            0 Jan 21 03:44 /var/log/nginx/error.log
	132174    4 drwx------   3 nginx    nginx        4096 Jan 21 03:44 /var/lib/nginx
	132175    4 drwx------   7 nginx    nginx        4096 Jan 21 03:44 /var/lib/nginx/tmp
	132173    4 drwx------   2 nginx    root         4096 Jan 21 03:44 /var/lib/nginx/tmp/client_body
	132219    4 drwx------   2 nginx    root         4096 Jan 21 03:44 /var/lib/nginx/tmp/proxy
	132221    4 drwx------   2 nginx    root         4096 Jan 21 03:44 /var/lib/nginx/tmp/uwsgi
	132222    4 drwx------   2 nginx    root         4096 Jan 21 03:44 /var/lib/nginx/tmp/scgi
	132220    4 drwx------   2 nginx    root         4096 Jan 21 03:44 /var/lib/nginx/tmp/fastcgi

**实例7：查找当前系统上没有属主或属组，且最近一个周内曾被访问过的文件**

	[root@bash ~]# find / -nouser -o -nogroup -a -atime -7

**实例8：查找/etc目录下大于1M且类型为普通文件的所有文件**

	[root@bash ~]# find /etc/ -size +1M -type f
	/etc/selinux/targeted/policy/policy.29
	/etc/udev/hwdb.bin

**实例9：查找/etc目录下所有用户都没有写权限的文件**

	[root@bash ~]# find /etc/ ! -perm /222
	/etc/pki/ca-trust/extracted/java/cacerts
	/etc/pki/ca-trust/extracted/openssl/ca-bundle.trust.crt
	/etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem
	/etc/pki/ca-trust/extracted/pem/email-ca-bundle.pem
	/etc/pki/ca-trust/extracted/pem/objsign-ca-bundle.pem
	/etc/lvm/profile/cache-mq.profile
	/etc/lvm/profile/cache-smq.profile
	/etc/lvm/profile/command_profile_template.profile
	/etc/lvm/profile/metadata_profile_template.profile
	/etc/lvm/profile/thin-generic.profile
	/etc/lvm/profile/thin-performance.profile
	/etc/openldap/certs/password
	/etc/gshadow
	/etc/dbus-1/system.d/cups.conf
	/etc/shadow
	/etc/gshadow-
	/etc/ld.so.conf.d/kernel-3.10.0-327.el7.x86_64.conf
	/etc/shadow-
	/etc/udev/hwdb.bin
	/etc/machine-id
	/etc/pam.d/cups
	/etc/sudoers

**实例10：查找/etc目录下至少有一类用户没有执行权限的文件**

	[root@bash ~]# find /etc/ ! -perm -111 # 至少有一类用户没有就是所有用户都没有

**实例11：.查找/etc/init.d目录下，所有用户都有执行权限，且其它用户有写权限的文件**

	[root@bash ~]# find /etc/init.d -perm -113
	/etc/init.d

或者

	[root@bash ~]# find /etc/init.d -perm -111 -perm -002
	/etc/init.d

**实例12：摩根定律找出/tmp目录下，属主不是root，且文件名不以f开头的文件**

	[root@centos7 ~]#find /tmp \( -not -user root -a -not -name 'f*' \) -ls
	即
	[root@centos7 ~]#find /tmp -not \( -user root -o -name 'f*' \) -ls

**实例13：查找/etc/下，除/etc/sane.d目录的其它所有.conf后缀的文件**

	[root@bash ~]# find /etc -path '/etc/sane.d' -prune -o -name '*.conf'

**实例14：匹配文件路径或文件**

	[root@bash ~]# find /usr/ -path '*local'
	/usr/bin/abrt-action-analyze-ccpp-local
	/usr/share/doc/postfix-2.10.1/examples/qmail-local
	/usr/share/aclocal
	/usr/libexec/postfix/local
	/usr/local

**实例15：基于正则表达式匹配文件路径**

	[root@bash ~]# find . -regex ".*txt$"              
	./.mozilla/firefox/4dqu966q.default/revocations.txt
	./vimrc/spf13-vim/LICENSE.txt
	./a.txt