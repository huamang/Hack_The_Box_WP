![在这里插入图片描述](https://img-blog.csdnimg.cn/759324f6923c4fef80105afde7be9248.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
nmap先嗦一把，开了22端口、80端口和1337端口
![在这里插入图片描述](https://img-blog.csdnimg.cn/b9ce20be701d418eba79ec56978f04d5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_17,color_FFFFFF,t_70,g_se,x_16)
访问是一个wordpress博客网站
![在这里插入图片描述](https://img-blog.csdnimg.cn/1821369c8b2f45f79cda3b2205200307.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
wpscan扫一下
版本是5.8.1
![在这里插入图片描述](https://img-blog.csdnimg.cn/c1881c36d63042c7b73bcb22fc651d5d.png)
再看看网站，这里会跳转backdoor.htb，配置一下hosts

```java
echo 10.10.11.125 backdoor.htb >> /etc/hosts
```

扫一下目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/83fda3980fa2479ca8dd050aae98ef3e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_17,color_FFFFFF,t_70,g_se,x_16)
这里有目录遍历
![在这里插入图片描述](https://img-blog.csdnimg.cn/a0bcae203f844179b70d9ccd4f4958c8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
这里有一个eBook-download的插件
在readme里面看到版本信息，找找看有没有什么漏洞
![在这里插入图片描述](https://img-blog.csdnimg.cn/f9414b36f87447ffb86722c3e35a670d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_19,color_FFFFFF,t_70,g_se,x_16)
版本好像是1.1
![在这里插入图片描述](https://img-blog.csdnimg.cn/a1ecbe9606c14c0dacbb9dbd01a74fd3.png)
的确有漏洞，任意文件下载
![在这里插入图片描述](https://img-blog.csdnimg.cn/9432d7b9a59d4e828b11a26b61c710a6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/5751d4d8812747a99e3cf7f8cc77f14f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
拿到一个数据库的账号密码
![在这里插入图片描述](https://img-blog.csdnimg.cn/2c9db0b24e86449eb62f2e66528fb0d6.png)

```java
wordpressuser   MQYBJSaD#DxG6qbm
```
看这密码就觉得有鬼，应该是想让我们连数据库
这里还有一个1337端口没用，但是不知道是什么服务，而本地文件包含有一个用法是

```java
/proc/[PID]/cmdline
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/96b300b906ed40bd91ba929933d96c2c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
所以我们可以进行这样的爆破，把正在运行的进程爆破出来，找到了开启了1337端口的服务，是gdbserver
![在这里插入图片描述](https://img-blog.csdnimg.cn/3c921477ae254bed890178a59c6e5050.png)
gdbserber有一个rce的漏洞，[链接](https://www.exploit-db.com/exploits/50539)
Metasploit有gbdserver的[模块](https://www.rapid7.com/db/modules/exploit/multi/gdb/gdb_server_exec/)

```
use exploit/multi/gdb/gdb_server_exec
```
配置好信息，默认是x86的，要换一个payload成x64的
![在这里插入图片描述](https://img-blog.csdnimg.cn/427041973223484cbb568127af8b97aa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
成功拿到shell，拿下用户权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/8a0fba5dda0c4023b60f516d35a5fb30.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
msf的shell太不稳定了，再手动弹一个

先用python弄一个交互shell，再反弹到kali里面
然后开一个python服务把linpeas传上去打
![在这里插入图片描述](https://img-blog.csdnimg.cn/90040e39e20c4b02b5be82e8f767a260.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
跑一下，是有一个CVE-2021-4034
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ef356b5c88a45ac9d31cd04a2be22b0.png)
跑了一下没跑出来，再找找思路
查看一下进程
```java
ps -elf
```
发现除了gdbserver还有一个screen，都是root开的
![在这里插入图片描述](https://img-blog.csdnimg.cn/094649cc7bf14508b4bc8bffa632ee0e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
screen有一个提权操作
>screen是Linux窗口管理器，用户可以建立多个screen会话，每个screen会话又可以建立多个window窗口，每一个窗口就像一个可操作的真实的ssh终端一样。
>使用文档：https://linux.die.net/man/1/screen

我们可以通过使用-x参数来进入到root的session中，直接设置TREM变量为xterm即可：

```java
export TERM=xterm
```

然后再切入root的session：

```java
screen -r root/root
```

这样就可以获得root权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/136269ea0fcd4822b208d003c317c7e2.png)