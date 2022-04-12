![image-20220410225018494](https://raw.githubusercontent.com/huamang/image/master/image-20220410225018494.png)

nmap进行端口扫描，开启了22和80端口

![image-20220411003612519](https://raw.githubusercontent.com/huamang/image/master/image-20220411003612519.png)

# USER



先看web服务，在80端口打开是helloworld

![image-20220411011723998](https://raw.githubusercontent.com/huamang/image/master/image-20220411011723998.png)

看一下源码，有注释

![image-20220411012515190](https://raw.githubusercontent.com/huamang/image/master/image-20220411012515190.png)

进去这个目录，是一个博客

![image-20220411012546277](https://raw.githubusercontent.com/huamang/image/master/image-20220411012546277.png)

随手测了一个admin目录，可以发现目录遍历

![image-20220411012740276](https://raw.githubusercontent.com/huamang/image/master/image-20220411012740276.png)

扫描一下目录

![image-20220411012840539](https://raw.githubusercontent.com/huamang/image/master/image-20220411012840539.png)

看一下这个readme文件，发现blog框架的版本号，找一下有没有可用漏洞

![image-20220411012824716](https://raw.githubusercontent.com/huamang/image/master/image-20220411012824716.png)

好像是有文件上传漏洞

![image-20220411012940182](https://raw.githubusercontent.com/huamang/image/master/image-20220411012940182.png)

这个是需要后台权限的，进入admin.php，测试默认密码，admin:nibbles

成功进入后台

![image-20220411014029870](https://raw.githubusercontent.com/huamang/image/master/image-20220411014029870.png)

在my image插件里面，可用进行任意文件上传，这里上传一个shell

![image-20220411014309520](https://raw.githubusercontent.com/huamang/image/master/image-20220411014309520.png)

webshell上传成功

```
nibbleblog/content/private/plugins/my_image/image.php
```

![image-20220411014701389](https://raw.githubusercontent.com/huamang/image/master/image-20220411014701389.png)

但是不知道为什么一直无法反弹shell，所以索性用msf了

![image-20220411021319699](https://raw.githubusercontent.com/huamang/image/master/image-20220411021319699.png)

成功拿到shell

![image-20220411021420502](https://raw.githubusercontent.com/huamang/image/master/image-20220411021420502.png)

用python获取一个交互shell

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

![image-20220411021630857](https://raw.githubusercontent.com/huamang/image/master/image-20220411021630857.png)

拿到user.txt

![image-20220411021755835](https://raw.githubusercontent.com/huamang/image/master/image-20220411021755835.png)

# ROOT

执行sudo -l

![image-20220411021832155](https://raw.githubusercontent.com/huamang/image/master/image-20220411021832155.png)

```
/home/nibbler/personal/stuff/monitor.sh
```

注意到了nibbler目录下还有一个personal.zip

直接base64去cat一下，然后解码保存下来

![image-20220411023358640](https://raw.githubusercontent.com/huamang/image/master/image-20220411023358640.png)

把压缩包打开，是下面的代码

```shell
                  ####################################################################################################
                  #                                        Tecmint_monitor.sh                                        #
                  # Written for Tecmint.com for the post www.tecmint.com/linux-server-health-monitoring-script/      #
                  # If any bug, report us in the link below                                                          #
                  # Free to use/edit/distribute the code below by                                                    #
                  # giving proper credit to Tecmint.com and Author                                                   #
                  #                                                                                                  #
                  ####################################################################################################
#! /bin/bash
# unset any variable which system may be using

# clear the screen
clear

unset tecreset os architecture kernelrelease internalip externalip nameserver loadaverage

while getopts iv name
do
        case $name in
          i)iopt=1;;
          v)vopt=1;;
          *)echo "Invalid arg";;
        esac
done

if [[ ! -z $iopt ]]
then
{
wd=$(pwd)
basename "$(test -L "$0" && readlink "$0" || echo "$0")" > /tmp/scriptname
scriptname=$(echo -e -n $wd/ && cat /tmp/scriptname)
su -c "cp $scriptname /usr/bin/monitor" root && echo "Congratulations! Script Installed, now run monitor Command" || echo "Installation failed"
}
fi

if [[ ! -z $vopt ]]
then
{
echo -e "tecmint_monitor version 0.1\nDesigned by Tecmint.com\nReleased Under Apache 2.0 License"
}
fi

if [[ $# -eq 0 ]]
then
{


# Define Variable tecreset
tecreset=$(tput sgr0)

# Check if connected to Internet or not
ping -c 1 google.com &> /dev/null && echo -e '\E[32m'"Internet: $tecreset Connected" || echo -e '\E[32m'"Internet: $tecreset Disconnected"

# Check OS Type
os=$(uname -o)
echo -e '\E[32m'"Operating System Type :" $tecreset $os

# Check OS Release Version and Name
cat /etc/os-release | grep 'NAME\|VERSION' | grep -v 'VERSION_ID' | grep -v 'PRETTY_NAME' > /tmp/osrelease
echo -n -e '\E[32m'"OS Name :" $tecreset  && cat /tmp/osrelease | grep -v "VERSION" | cut -f2 -d\"
echo -n -e '\E[32m'"OS Version :" $tecreset && cat /tmp/osrelease | grep -v "NAME" | cut -f2 -d\"

# Check Architecture
architecture=$(uname -m)
echo -e '\E[32m'"Architecture :" $tecreset $architecture

# Check Kernel Release
kernelrelease=$(uname -r)
echo -e '\E[32m'"Kernel Release :" $tecreset $kernelrelease

# Check hostname
echo -e '\E[32m'"Hostname :" $tecreset $HOSTNAME

# Check Internal IP
internalip=$(hostname -I)
echo -e '\E[32m'"Internal IP :" $tecreset $internalip

# Check External IP
externalip=$(curl -s ipecho.net/plain;echo)
echo -e '\E[32m'"External IP : $tecreset "$externalip

# Check DNS
nameservers=$(cat /etc/resolv.conf | sed '1 d' | awk '{print $2}')
echo -e '\E[32m'"Name Servers :" $tecreset $nameservers 

# Check Logged In Users
who>/tmp/who
echo -e '\E[32m'"Logged In users :" $tecreset && cat /tmp/who 

# Check RAM and SWAP Usages
free -h | grep -v + > /tmp/ramcache
echo -e '\E[32m'"Ram Usages :" $tecreset
cat /tmp/ramcache | grep -v "Swap"
echo -e '\E[32m'"Swap Usages :" $tecreset
cat /tmp/ramcache | grep -v "Mem"

# Check Disk Usages
df -h| grep 'Filesystem\|/dev/sda*' > /tmp/diskusage
echo -e '\E[32m'"Disk Usages :" $tecreset 
cat /tmp/diskusage

# Check Load Average
loadaverage=$(top -n 1 -b | grep "load average:" | awk '{print $10 $11 $12}')
echo -e '\E[32m'"Load Average :" $tecreset $loadaverage

# Check System Uptime
tecuptime=$(uptime | awk '{print $3,$4}' | cut -f1 -d,)
echo -e '\E[32m'"System Uptime Days/(HH:MM) :" $tecreset $tecuptime

# Unset Variables
unset tecreset os architecture kernelrelease internalip externalip nameserver loadaverage

# Remove Temporary Files
rm /tmp/osrelease /tmp/who /tmp/ramcache /tmp/diskusage
}
fi
shift $(($OPTIND -1))

```

我们直接unzip解压这个压缩包，进去执行这个sh文件

把命令写进sh文件中去

![image-20220411023718782](https://raw.githubusercontent.com/huamang/image/master/image-20220411023718782.png)

然后sudo执行sh文件，那到root的flag

