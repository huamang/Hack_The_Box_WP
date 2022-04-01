# 前言
久闻HTB平台大名，这里不需要下载镜像自己打了，可以直接在网上开好镜像，连上vpn直接干
这次就算是对这个平台的一个熟悉操作吧
# Meow
下载到了open文件，kali连接上
![在这里插入图片描述](https://img-blog.csdnimg.cn/75b6bc63d8f64b4882d2976b67cfb14a.png)
然后测试一下可以ping通了
![在这里插入图片描述](https://img-blog.csdnimg.cn/80be90cc9f674d9484d6158b62f4de30.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_16,color_FFFFFF,t_70,g_se,x_16)
然后就开nmap跑

```
nmap -sV 10.129.160.90
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/91c1dc48a1c54459b6cd5921f84f6b0d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
开启了23端口，telnet服务，直接连接，root账户
![在这里插入图片描述](https://img-blog.csdnimg.cn/2845c457ce0c483cb190dcae0060e5c5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_19,color_FFFFFF,t_70,g_se,x_16)
直接拿flag
![在这里插入图片描述](https://img-blog.csdnimg.cn/87a234e8524f4bfaa06ad0a6be104f2d.png)
这个算是白给的
# Paper
下面就开始打正式的靶机了
一开始开启靶机ping不通，发现Starting Point和Machines居然不是用的一个
![在这里插入图片描述](https://img-blog.csdnimg.cn/6e80e455d47c407a91d09ed92a0f81fc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_13,color_FFFFFF,t_70,g_se,x_16)
所以需要重新配置一下openvpn
![在这里插入图片描述](https://img-blog.csdnimg.cn/cc994dcc5747463db858d2f3757b7507.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
拿到IP地址
![在这里插入图片描述](https://img-blog.csdnimg.cn/ec2a891e746e4f7295902027cabc15df.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
先nmap嗦一把，可以看到开启了22，80，443端口
![在这里插入图片描述](https://img-blog.csdnimg.cn/cd19a343042742eba7b0cea82faececf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
80端口开了一个web服务
![在这里插入图片描述](https://img-blog.csdnimg.cn/abce0e9c3ea044fc8da302dee8b332e3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
看响应头把域名配上去，/etc/hosts
![在这里插入图片描述](https://img-blog.csdnimg.cn/cba129f03d634cf69e6116e6976ac4a4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/bcf38f5891b84443a81f4de0f8168ee0.png)

## USER
是一个WordPress网站
![在这里插入图片描述](https://img-blog.csdnimg.cn/1bdf824acc2343fda7bd44890c943cef.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
这里用wpscan扫一下， 版本是5.2.3
![](https://img-blog.csdnimg.cn/3307adc3acdd434ca309caaafd0b8f50.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
谷歌搜了一下，这个版本有一个未授权查看博客的漏洞
![在这里插入图片描述](https://img-blog.csdnimg.cn/e2da446858054324add2ebe11a0ed898.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
这里果然发现了一篇隐藏文章
![在这里插入图片描述](https://img-blog.csdnimg.cn/c5f73ff92cc842b584f390d9048559bf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
说了一大堆，这里有一个重点
有一个秘密聊天网站
```php
# Secret Registration URL of new Employee chat system
http://chat.office.paper/register/8qozr226AhkCHZdyY
```
把域名加入到hosts，然后再进去，发现是一个注册的页面，那么就注册一个账号来
![在这里插入图片描述](https://img-blog.csdnimg.cn/1d56e870062d43a4ba073acd02836738.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/f0f70600e7de492b8336c048d44661d4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
在里面最特别的一个npc就是这个机器人，看到这一条
![在这里插入图片描述](https://img-blog.csdnimg.cn/860bba43ebd541179737f2a53c8adc6b.png)
看到这里就知道这里可以做点什么了
![在这里插入图片描述](https://img-blog.csdnimg.cn/396f4ad46c0b4b0f8c3a97a1722f8a60.png)
的确可以做到任意文件读取
![在这里插入图片描述](https://img-blog.csdnimg.cn/f5497452a5e043f19a66fedda4ef667e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_16,color_FFFFFF,t_70,g_se,x_16)

```php
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin
systemd-resolve:x:193:193:systemd Resolver:/:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
polkitd:x:998:996:User for polkitd:/:/sbin/nologin
geoclue:x:997:994:User for geoclue:/var/lib/geoclue:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin
qemu:x:107:107:qemu user:/:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
cockpit-ws:x:996:993:User for cockpit-ws:/:/sbin/nologin
pulse:x:171:171:PulseAudio System Daemon:/var/run/pulse:/sbin/nologin
usbmuxd:x:113:113:usbmuxd user:/:/sbin/nologin
unbound:x:995:990:Unbound DNS resolver:/etc/unbound:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
gluster:x:994:989:GlusterFS daemons:/run/gluster:/sbin/nologin
chrony:x:993:987::/var/lib/chrony:/sbin/nologin
libstoragemgmt:x:992:986:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
saslauth:x:991:76:Saslauthd user:/run/saslauthd:/sbin/nologin
dnsmasq:x:985:985:Dnsmasq DHCP and DNS server:/var/lib/dnsmasq:/sbin/nologin
radvd:x:75:75:radvd user:/:/sbin/nologin
clevis:x:984:983:Clevis Decryption Framework unprivileged user:/var/cache/clevis:/sbin/nologin
pegasus:x:66:65:tog-pegasus OpenPegasus WBEM/CIM services:/var/lib/Pegasus:/sbin/nologin
sssd:x:983:981:User for sssd:/:/sbin/nologin
colord:x:982:980:User for colord:/var/lib/colord:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
setroubleshoot:x:981:979::/var/lib/setroubleshoot:/sbin/nologin
pipewire:x:980:978:PipeWire System Daemon:/var/run/pipewire:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
gnome-initial-setup:x:979:977::/run/gnome-initial-setup/:/sbin/nologin
insights:x:978:976:Red Hat Insights:/var/lib/insights:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
mysql:x:27:27:MySQL Server:/var/lib/mysql:/sbin/nologin
nginx:x:977:975:Nginx web server:/var/lib/nginx:/sbin/nologin
mongod:x:976:974:mongod:/var/lib/mongo:/bin/false
rocketchat:x:1001:1001::/home/rocketchat:/bin/bash
dwight:x:1004:1004::/home/dwight:/bin/bash
```
还有一个功能叫list，可以看目录文件，类似于ls
![在这里插入图片描述](https://img-blog.csdnimg.cn/418b887b0a6d4db8ae15bd50153b3e41.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/84cd3a6650ec40ba9758a4e28172b7c1.png)
再看看上一层的目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/81a6b730145a4a099c297a9a0727c47c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_15,color_FFFFFF,t_70,g_se,x_16)
翻翻找找，在这个文件里面找到了一个密码
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba8ba394718f48a69d79d92eeb9faa04.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_13,color_FFFFFF,t_70,g_se,x_16)
```php
export ROCKETCHAT_USER=recyclops
export ROCKETCHAT_PASSWORD=Queenofblad3s!23
```
用这个登录了一下，他说这个机器人不能在web里登陆，那就试试ssh是不是共用了一个密码
试一下果然是
![在这里插入图片描述](https://img-blog.csdnimg.cn/03d6e44bf659480fa6d68ab392ff4ce6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_19,color_FFFFFF,t_70,g_se,x_16)
拿到第一个flag
fcb715124fb8598cb1e1295c2c9c3228
![在这里插入图片描述](https://img-blog.csdnimg.cn/d350198e9400449989d76faed573777d.png)
## ROOT
接下来就是提权了
试了一下，好像htb的靶机不出网哦
![在这里插入图片描述](https://img-blog.csdnimg.cn/e18e4dbfd7114372a3b4fdd9e8e66b3d.png)
那就本地搞上去，开一个python服务

![在这里插入图片描述](https://img-blog.csdnimg.cn/0fc85af2f84344fba6ea2e324e42ed6a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
然后wget下载下来就可以了
![在这里插入图片描述](https://img-blog.csdnimg.cn/cf0b42aa5a1a4bdb825e030573668e20.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
`chmod +x linpeas.sh`加一个权限，成功跑起来了
![在这里插入图片描述](https://img-blog.csdnimg.cn/e1a17080fc7d4528a7fae274140c8781.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
跑出是一个CVE-2021-3560
![在这里插入图片描述](https://img-blog.csdnimg.cn/37ba32f74566421c875c0ee8dac1e262.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaHVhbWFuZ2dn,size_20,color_FFFFFF,t_70,g_se,x_16)
github找到提权[脚本](https://github.com/Almorabea/Polkit-exploit)
提权成功，拿下flag
![在这里插入图片描述](https://img-blog.csdnimg.cn/1ade3a59574d47b6bf46b76bd354c99c.png)