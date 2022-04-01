![在这里插入图片描述](https://img-blog.csdnimg.cn/9a001f0fdfc24f0488d29ee76a928379.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
先namp扫描端口，开启了21，22，139，445、3632
![在这里插入图片描述](https://img-blog.csdnimg.cn/ebf7832410f741aeb94031d7c1f15079.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
没有http服务。。看这里ftp搜集到很多信息，那就从ftp下手
之类是vsFTPd 2.3.4
![在这里插入图片描述](https://img-blog.csdnimg.cn/eb7fe2882b144fcc9aa1e8a8810b2fc3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_17,color_FFFFFF,t_70,g_se,x_16)
找了一下漏洞，果然有
![在这里插入图片描述](https://img-blog.csdnimg.cn/94ad0d3e6ae94e6194da25faef6b72c0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
msf打了一波，但是没用，打不通不知道为啥
![在这里插入图片描述](https://img-blog.csdnimg.cn/c7ce39df7aea4b9baa9177084eaed5e2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
这里还有一个服务，samba 3.0.20
![在这里插入图片描述](https://img-blog.csdnimg.cn/9bdc5f4281024f90a0a18b3d365e5ce7.png)
msf有这个漏洞可以利用
![在这里插入图片描述](https://img-blog.csdnimg.cn/e1fc42acfd7b4355ae3d38fa42144cce.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

成功rce
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff4c4b4293fc468aab41be574ef8a877.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
直接拿到shell就是root
![在这里插入图片描述](https://img-blog.csdnimg.cn/5ead2ff2e215418085386b07395bf21e.png)

一口气拿掉两个flag
![在这里插入图片描述](https://img-blog.csdnimg.cn/7c23d0f0011348aca0bb1e1288939013.png)