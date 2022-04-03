![image-20220403165705019](https://raw.githubusercontent.com/huamang/image/master/image-20220403165705019.png)

# USER

拿到ip先进行nmap扫描端口，开启了21，22，80端口

![image-20220403172139435](https://raw.githubusercontent.com/huamang/image/master/image-20220403172139435.png)

先去80端口看看，看起来是一个后台

![image-20220404000552682](https://raw.githubusercontent.com/huamang/image/master/image-20220404000552682.png)

有一个模块可以下载流量包，直接点进去是没有内容

![image-20220404005421058](https://raw.githubusercontent.com/huamang/image/master/image-20220404005421058.png)

注意到了url这里写的是2，试着访问1和0，在0处发现了数据

![image-20220404011754258](https://raw.githubusercontent.com/huamang/image/master/image-20220404011754258.png)

下载下来分析流量包，追踪TCP流，发现ftp的明文密码

![image-20220404012029752](https://raw.githubusercontent.com/huamang/image/master/image-20220404012029752.png)

```
nathan
Buck3tH4TF0RM3!
```

连接FTP服务，成功进去

![image-20220404012433333](https://raw.githubusercontent.com/huamang/image/master/image-20220404012433333.png)

# ROOT

用这个账号密码试着ssh连接进服务器，果然登录进去了

![image-20220404020752929](https://raw.githubusercontent.com/huamang/image/master/image-20220404020752929.png)

![image-20220404025257683](https://raw.githubusercontent.com/huamang/image/master/image-20220404025257683.png)

用python3弹一个shell，拿到rootshell

![image-20220404033255766](https://raw.githubusercontent.com/huamang/image/master/image-20220404033255766.png)