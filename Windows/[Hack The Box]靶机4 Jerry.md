![在这里插入图片描述](https://img-blog.csdnimg.cn/638ee9bf115a49fb8232f709ef3df85e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
首先用nmap进行端口扫描，看到只开启了8080端口，是Tomcat
![在这里插入图片描述](https://img-blog.csdnimg.cn/ed62144172b1432e85dc0f30a981517f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
给出的是默认界面
![在这里插入图片描述](https://img-blog.csdnimg.cn/9c9da8f16487411883311464c171035e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
dirmap扫描了一下目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/d2d04858f37e4ef48b46720e2e9a3a65.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

去manager管理页面看看，他需要认证
![在这里插入图片描述](https://img-blog.csdnimg.cn/e0beda14383845cbbd163945addd5fb9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/d405299c828a4fd2b520508eb9df661d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
他提示密码为s3cret，试了一下真是这个，倒省事了，不用我自己爆破
![在这里插入图片描述](https://img-blog.csdnimg.cn/b6a359b80b624bbb9cd425d0abf21c19.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
进tomcat的后台就好弄了哦，getshell方法多的是，[这里](https://blog.csdn.net/weixin_41924764/article/details/108196725)写的挺多的，这里我选择直接用是msf打，方便点

```
use exploit/multi/http/tomcat_mgr_upload
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c4c26185b199440087393912dc11e2be.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
直接打通
![在这里插入图片描述](https://img-blog.csdnimg.cn/0878762fd8d84433876c4fa3db64aef7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
在Administrator的桌面发现flag
![在这里插入图片描述](https://img-blog.csdnimg.cn/1e08e6f4cd4c4c7487b4471cab755776.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

```
cd C:\Users\Administrator\Desktop\flags
type "2 for the price of 1.txt"
```
拿下
![在这里插入图片描述](https://img-blog.csdnimg.cn/1fa1e2da120a4369bba6d19c65c0e6d7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)