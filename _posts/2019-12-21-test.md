---
layout: post
title:ReSpeaker Core v2开发文档学习
tags: Embedded system
stickie: true
---

# ReSpeaker Core v2开发文档学习

<img src="https://s2.ax1x.com/2019/12/21/QvhcAU.png" alt="image-20191221182130706" style="zoom: 80%;" />

① 3.5mm 耳机插孔

② USB OTG : 此USB 端口用于通过 putty (或其他串口工具) 的串口模式连接计算机（原理去了解一下）

③ USB 电源输入（供电）。

④ 扬声器插孔 : 用于无源音响的输出音频。Jst 2.0 插座。

⑤ UART : 可以通过此 UART 端口将 ReSpeaker Core v2.0 与计算机连接。（异步串口通信协议，嵌入式课学过原理，再写一个详悉）

⑥ 8 GPIO 引脚 

⑦ SD 卡槽 : 插入 micro-SD 卡（烧写系统）

⑧ eMMC : Embedded Multi Media Card。可以将镜像刻录到 eMMC 中，这样 ReSpeaker Core v2.0 可以从 eMMC 引导。

⑨ USB Host : 可以通过这两个 USB Host 将 USB 设备 (如 USB 鼠标，USB 键盘和 USB 闪存盘) 插入ReSpeaker Core v2.0。

Ⓐ Ethernet : 访问互联网（插网线的地方）

Ⓑ HDMI : output video（和电脑上的HDMI接口一样）

Ⓒ Bluetooth 及 WIFI 天线 : 用于 WIFI 和蓝牙的板载天线。

Ⓓ Grove 接口 : 用于数字或 I2C 的 Grove 接口（我来强行理解一下，使用这个接口通过线缆再接一个板子）。

官网文档：基本单元（通常为微处理器）允许从Grove模块轻松连接任何输入或输出。每个Grove模块通常都可以处理单个功能，例如简单的按钮或更复杂的心率传感器。

入门：

1.镜像安装：从SD卡启动或从eMMC启动，具体操作下载见文档http://wiki.seeedstudio.com/ReSpeaker_Core_v2.0/#hardware-overview

2.连接计算机，两种方法，通过UART连接（上图5）/通过OTG连接（上图2）

UART连接需要USB转TTL适配器，没有，不做。

<img src="C:\Users\18125\Desktop\webcamera\4.png" alt="4" style="zoom:50%;" />

一、OTG连接：

**步骤** **1****.** 检查计算机串口是否启用 :

Windows : 检查设备管理器，应该有新的串行设备名为 COMx，其中 x 是一个越来越大的数字。如果使用 windows XP/⅞，也许需要安装 windows CDC 驱动程序。（目测win10也要，且网上没教程）

Linux : ls /dev/ttyACM*，应该得到 /dev/ttyACMx 其中 x 取决于您使用的 USB 端口.

Mac : ls /dev/cu.usb*，应该得到 /dev/cu.usbmodem14xx 其中 xx 取决于您使用的 USB 端口。

大坑分割线

------

**检查不到以COMx设备名**

1.设备管理器——查看——显示隐藏的设备

2.删除占用端口（比如：当你发现你的一个端口是灰色的，且这个端口位置在hub2，但是现在你的开发板已经连接上了，这说明有一个以前的设备（灰色）占着开发板的端口）。

3.下载驱动程序，但是没有数字签名安装不上

4.更新和安全——恢复——高级启动——立即重启——疑难解答——高级选项——启动设置——重启——输入7（禁用驱动程序强制签名）

5.找到下载的驱动程序，直接安装，这就完成了？naive！设备管理器去找那个因为驱动而gg的硬件（也就是我们的小板子，可能会有一个黄色的感叹号，反正我的有）

6.右键——更新驱动程序——浏览我的计算机以查找驱动程序软件——让我从计算机的可用驱动程序列表中选取——找到对应驱动，完成！

------

步骤 2. 使用您最喜欢的串口调试工具来连接串口，串口有 : 115200 波特率，8位，奇偶校验无，停止位 1，流量控制无。举些例子 :

Windows : 使用 PUTTY，选择 Serial 协议，填入 ReSpeaker Core v2.0 对应的 COM 端口，115200 波特率，8 位，奇偶校验无，停止位 1，流量控制无。

Linux : 取决于 USB To TTL Adapter，应该是 screen /dev/ttyACM0(,1, and so on) 115200 或 screen /dev/ttyUSB0(,1, and so on) 115200

Mac : 取决于 USB To TTL Adapter，应该是 screen /dev/cu.usbserial1412(,1422, and so on) 115200 或 screen /dev/cu.usbmodem1412(,1422, and so on) 115200

小坑：这里的serial在session中选

**步骤** **3****.** 默认用户名是 respeaker，密码也是 respeaker。

二、网络设置

**A. Wi-Fi 设置**

通过网络管理工具 nmtui 配置 ReSpeaker 的网络，nmtui 已经安装在 ReSpeaker 的镜像上。

respeaker@v2:~$ sudo nmtui

然后会看到这样的配置页面，选择 Activate a connection 并按下 Enter 键。

<img src="C:\Users\18125\Desktop\webcamera\1.png" alt="1"  />

为 ReSpeaker v2.0 选择 Wi-Fi，选 Enter 键并输入 Wi-Fi 密码，然后再次选 Enter 键。当看到 * 标记时，表示 ReSpeaker 已成功连接到 Wi-Fi 网络。选 Esc 键两次离开网络管理工具。（**校园网不可**，手机热点可）

<img src="C:\Users\18125\Desktop\webcamera\3.png" alt="3" style="zoom:50%;" />

<img src="C:\Users\18125\Desktop\webcamera\5.png" alt="5" style="zoom:50%;" />

ip：192.168.43.176

除了有用户界面的网络管理工具外，还有一个命令行式网络管理工具。如果连接到隐藏的 Wi-Fi 网络，则需要使用以下命令行工具 :

nmcli c add type wifi con-name mywifi ifname wlan0 ssid your_wifi_ssid
 nmcli con modify mywifi wifi-sec.key-mgmt wpa-psk
 nmcli con modify mywifi wifi-sec.psk your_wifi_password
 nmcli con up mywifi

（没试）

B.以太网连接，直接在路由器和板子间插网线

三、通过 SSH & VNC 连接

板子与PC应连接同一个wifi

SSH：putty选择session中的SSH协议，输入上文ip地址，登录名密码同上（respeaker）

VNC：打开VNC地址栏搜索上文ip，输入密码同上，即可看到图形界面

四、蓝牙设置

除了作为蓝牙音响使用外，它还可以作为蓝牙播放器来连接蓝牙耳机或蓝牙音响。

**步骤 1.** 输入 bluetoothctl 打开蓝牙对话框。

**步骤 2.** 输入 scan on 扫描蓝牙设备。

**步骤 3.** 当 ReSpeaker Core v2.0 找到您的目标设备时，输入 scan off。

PS：我为啥要在图书馆做这件事？？？眼睛都要找瞎了才找见，QAQ

<img src="C:\Users\18125\Desktop\webcamera\6.png" alt="6" style="zoom:50%;" />

**步骤 4.** 现在使用命令 pair + device ID 将蓝牙设备与 ReSpeaker Core v2.0 匹配。

**步骤5.** 当看到消息 Pairing successful 时，输入 connect + device ID。

<img src="C:\Users\18125\Desktop\webcamera\7.png" alt="7" style="zoom:50%;" />

可以输入 exit 或 quit 以退出 shell，然后使用以下命令测试蓝牙设备。

arecord bluetoothtest.wav
 aplay bluetoothtest.wav

（最简单测试：连手机蓝牙，直接拿手机播放音乐叭）

五、录音和播放

1.通过ALSA测试

通过以下命令检查正确的设备索引。

找到名称有seeed前缀的声卡。在上面的例子中，捕获设备是hw:0,0，这意味着 card 0/device 0。回放设备是 hw:0,1，这意味着 card 0/device 1。然后通过以下命令测试录音和播放声音 :

<img src="C:\Users\18125\Desktop\webcamera\8.png" alt="8" style="zoom:50%;" />

A.录制和播放双声道音频：

arecord -Dhw:0,0 -f S16_LE -r 16000 -c 2 hello.wav

aplay -Dhw:0,1 -r 16000 -c 2 hello.wav

B.通过蓝牙设备输出声音：

aplay -r 16000 -c 2 hello.wav 

C.录制8声道音频,机上有6个麦克风，其余2个频道由AC108组成。

arecord -Dhw:0,0 -f S16_LE -r 16000 -c 8 hello_8ch.wav

D.同时录制和播放。

arecord | aplay

**2. 通过 PulseAudio 测试**

首先检查 PulseAudio 是否在运行 :

respeaker@v2:~$ ps aux|grep pulse|grep -v grep
 respeak+ 1109 0.0 0.7 363272 7932 ?    S<l 01:01  0:00 /usr/bin/pulseaudio --start --log-target=syslog

如果没有，请参阅 PulseAudio 的文档以启用 PulseAudio 的自动生成。然后测试 : 

parecord --channels=8 --rate=16000 --format=s16le hello2.wav
 paplay hello2.wav

此外，默认的 ALSA 设备现在挂接到 PulseAudio，因此还可以通过 PulseAudio 使用以下命令播放/记录声音 : 

arecord -v -f cd hello3.wav
 aplay hello3.wav

又是坑的分界线

------

这次太太太坑了，为无知付出了惨痛代价。

**为啥winSCP登录不了？为什么ping不通开发板？为什么SSH连接超时？**

因为——电脑连接的wifi和板子连接的wifi不是同一个。。。

在弱智的道路上越走越远了

解决开发板ping不通主机的原因与办法：

   第一,先检查一些自己的开发板与主机的ip是否在同一网段。这种是最可能忽视的原因,因为新手可能也是第一次尝试这种ping通。检查一下，如果开发板与主机ip在同一网段但是还是不能ping通主机,则可以继续看一下以下的原因和解决办法。

   第二,自己的主机开启了防火墙功能。这种情况也是新手容易忽视的,因为我们平时的防火墙都是开启的,所以看看你的防火墙是否已经关了。

   第三,查看主机能否ping通其他的计算机。如果主机不能ping通其他的计算机,那说明自己的主机可能有问题。可以先着重解决主机的问题，或者换一台主机来进行开发板ping主机。

   第四,将linux虚拟机设置为桥接。有些新手在选择是没有注意选择桥接而导致开发板ping不通主机。

最近感想分界线

------

在知乎上看了一篇问答，导师遇见最令人崩溃的学生是什么样，里面有导师吐槽学生也有研究生吐槽师弟师妹的那种，我特别庆幸我遇到的问题自己墨迹墨迹就解决了，这些问题要是问出去的话，估计我导和我师兄就要去吐槽我了。。。我可以去回答经常被自己蠢哭是什么感受QAQ