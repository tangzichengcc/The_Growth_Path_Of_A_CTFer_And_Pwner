# *ctf2022-web-oh-my-grafana

# 学到的东西

1.fuzz ,自动化fuzz可能存在的文件,这个需要自己积累,以及提前准备好!包括工具和字典.

2.漏洞库的准备,开始时可以进行快速搜索

对于一些针对开源的软件或者有安装包的,可以自己搭建本地环境,来查看可能存在的漏洞

题目一上来是一个grafana的登陆页面,这个之前有接触过,这个应用存在漏洞.

点击forget password后可以看到,下面给到了版本

![Untitled](ctf2022-we%20a8545/Untitled.png)

![Untitled](ctf2022-we%20a8545/Untitled%201.png)

在进行一番信息收集后,通过searchsploit和搜索引擎搜索,发现存在一个任意文件读取漏洞.

[https://blog.csdn.net/qq_45244158/article/details/121836825](https://blog.csdn.net/qq_45244158/article/details/121836825)

![Untitled](ctf2022-we%20a8545/Untitled%202.png)

确认漏洞存在.

![Untitled](ctf2022-we%20a8545/Untitled%203.png)

这里可以对可能存在的文件进行一个fuzz

github有一些资源,不过这个也不一定全,还需要自己平常积累收集

## 任意文件读取阶段

常规文件没有读取到什么有用的,然后就可以对grafana特定的目录进行扫描.

这个阶段一方面可以搜索一下常见目录和文件的路径,另一方面可以自己本地搭建一下,然后找一下路径

### grafana的文件目录

[https://blog.csdn.net/qq_41181590/article/details/107658141](https://blog.csdn.net/qq_41181590/article/details/107658141)

这里踩了个大坑,因为能读取到数据存储文件,就一直死磕这个,里面存着admin的密码,但是是加密的,还有随机值和盐值,解密不了.浪费了大量时间.

/var/lib/grafana/grafana.db

### 峰回路转

后来才发现,其实就在配置文件里,(所以说配置文件很重要呀

/etc/grafana/grafana.ini

要对读取的特殊文件进行仔细的搜索,其实这里当时也搜索了,可能因为粗心略过了....

![Untitled](ctf2022-we%20a8545/Untitled%204.png)

### 下一步

然后就登陆进去了,但是之前没用过这玩意,所以先自己探索一下,还有利用搜索引擎,进行一个简单的学习

搜索了一堆没有发现flag,学习后发现它的后台有连接数据库,所以flag应该存在数据库里

![Untitled](ctf2022-we%20a8545/Untitled%205.png)

配置中发现了mysql数据库

![Untitled](ctf2022-we%20a8545/Untitled%206.png)

### 数据库操作

然后在explore这里可以进行数据库的查询,这里懵了一会,因为看不太懂这个东西,后来发现在右上角那个小铅笔按钮✏️,可以转换成原始sql语句,然后就可以直接用sql语句查找flag了 

![Untitled](ctf2022-we%20a8545/Untitled%207.png)

查找数据库

select database()   注意format选择table

![Untitled](ctf2022-we%20a8545/Untitled%208.png)

查找数据表

SELECT table_name FROM information_schema.tables WHERE TABLE_SCHEMA = 'grafana’

![Untitled](ctf2022-we%20a8545/Untitled%209.png)

查找flag

select * from fffffflllllllllaaaagggggg;

![Untitled](ctf2022-we%20a8545/Untitled%2010.png)
