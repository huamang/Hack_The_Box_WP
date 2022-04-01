![](https://img-blog.csdnimg.cn/faa05bb233ff473083067725dbb54bae.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
老规矩，namp一把嗦扫端口
开启了139，445和3389，web都没开一个，那肯定得从其他方面下手了
![在这里插入图片描述](https://img-blog.csdnimg.cn/01fb86a8c4404196b938e8b9fdc20e8e.png)
从namp结果看起来，貌似得从smb这里下手
![在这里插入图片描述](https://img-blog.csdnimg.cn/e7cc2dc54bbb406da3f3cdc4594d4c4a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
直接用nmap进行漏扫

```java
nmap --script=vuln 10.10.10.4
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/e1bc3f88b4f94a67894f78f8d55f74de.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
这里都直接扫出来了，ms17-010，msf可以打的

![在这里插入图片描述](https://img-blog.csdnimg.cn/7d5e2acd4b3a4afba46119e8a7a96ecb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
直接拿到shell
![在这里插入图片描述](https://img-blog.csdnimg.cn/99819ff8b81c42a2b8abcef9851630e8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
权限也都是admin，直接拿下两个flag
![在这里插入图片描述](https://img-blog.csdnimg.cn/37f8629751044af68e9337d6a919b061.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/e52c44d0901e4de78c6701f6832eb7fb.png)

# Blue
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c2520b231534e149341716fc2e0d3a0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
nmap扫了一下，开启了135，139，445
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ca6a26104684006b72e0b06fcf33b62.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_18,color_FFFFFF,t_70,g_se,x_16)
漏扫了一下，还是可以直接ms17-010
![在这里插入图片描述](https://img-blog.csdnimg.cn/25077f40e8e24a909dccca44e066a5b1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
msf直接一把嗦了，又是白给的靶机

![在这里插入图片描述](https://img-blog.csdnimg.cn/7c5ab4c53408476ebe5464f1b76b3a80.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/a948dcf8f5f742fc8f1d0c6c3878e946.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/fdd2f294d8fe47be9b6f1f042121719f.png)