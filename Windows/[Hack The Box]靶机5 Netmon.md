![在这里插入图片描述](https://img-blog.csdnimg.cn/a824298e97334c668e9e3eeedc45200a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
先nmap扫描端口，还是开了挺多端口的
![在这里插入图片描述](https://img-blog.csdnimg.cn/8305a0dcb2e541f981cbbff17bc2ba9a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_18,color_FFFFFF,t_70,g_se,x_16)

先看80端口吧
![在这里插入图片描述](https://img-blog.csdnimg.cn/92e86613f9af4be6b0cdd5c2cd60d1bc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
找了一圈，好像没啥明显的漏洞点，忘记密码处可以用户名枚举
再看看ftp吧，看nmap扫出来了
![在这里插入图片描述](https://img-blog.csdnimg.cn/f4a1df241d8443beb2c57b99ecddbc3a.png)
果然登进去了
![在这里插入图片描述](https://img-blog.csdnimg.cn/bcb6c9213c1e4db59a5ee8ba74067305.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
在users/public拿到user.txt
![在这里插入图片描述](https://img-blog.csdnimg.cn/e8a17dd6d0a0452b969d2a8096776c7c.png)
找一下web目录
在users/all users/paessler/PRTG Network Monitor
![在这里插入图片描述](https://img-blog.csdnimg.cn/3f2ac9853799401aa3f17143d8acf1cb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
把一个这个文件脱下来，应该是旧版本的备份文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/9bf88b9fcd8145ac8be7ec5c48474c08.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

找到一个密码
![在这里插入图片描述](https://img-blog.csdnimg.cn/18ceed7397e245bebf27d0cb87e89246.png)

```
prtgadmin
PrTg@dmin2018
```
试了一下没登进去
![在这里插入图片描述](https://img-blog.csdnimg.cn/0cc40a3afb81479bba1ce2047d064ac0.png)
后来想到这个是旧版本的，看日期还是18年的，最新版是19年的，试着改成19

![在这里插入图片描述](https://img-blog.csdnimg.cn/cef6c039d6ca4bf6a07d822562076437.png)

```
prtgadmin
PrTg@dmin2019
```
登进去了
![在这里插入图片描述](https://img-blog.csdnimg.cn/f7b8ee7598d048ed9240bdb155e77c21.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
这个系统版本是18.1
![在这里插入图片描述](https://img-blog.csdnimg.cn/23ef3234e93444de98e2ae166b49bfe6.png)
这个版本有一个cve
![在这里插入图片描述](https://img-blog.csdnimg.cn/e43a311b0dd049aebec49f013d5a762f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
我用的是这个exp
https://www.exploit-db.com/exploits/46527
把cookie放上去就可以直接打了
![在这里插入图片描述](https://img-blog.csdnimg.cn/c080e208a052469a9f3cc034aa337e51.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
帮我们建立了一个administrator的账户

```
pentest
P3nT3st!
```
然后就是连接上机子了，但是没开3389，不能桌面连接
试着用psexec连接，但是说账号密码错误连不上。。。不靠谱啊这个脚本
直接手动操作吧
看这篇文章：https://www.codewatch.org/blog/?p=453
![在这里插入图片描述](https://img-blog.csdnimg.cn/8aa9780df30c444d99d22e201bc68657.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

```
test.txt;net user anon p3nT3st! /add;net localgroup administrators anon /add
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/28bd16ee33e947309ebcdc7d137eb693.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

再触发一下
![在这里插入图片描述](https://img-blog.csdnimg.cn/a621ff23fec548f988ce5cb425aef6a0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/1989522f1216463d83f3411759171b8e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_16,color_FFFFFF,t_70,g_se,x_16)
成功连进去了，但是网络问题，很容易掉
![在这里插入图片描述](https://img-blog.csdnimg.cn/52fa849da8ff4a21b9bdd1ed71fb52b3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
换了一个winrm去连接，终于连接上了
![在这里插入图片描述](https://img-blog.csdnimg.cn/ebd4a55ec3b14ceea62d483844142575.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
拿下root
![在这里插入图片描述](https://img-blog.csdnimg.cn/6152fa613c2a4e398049f22d1dafa3a9.png)