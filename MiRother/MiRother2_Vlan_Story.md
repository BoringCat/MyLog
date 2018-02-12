## 小米路由器R2D的VLAN与路由的问题
### 前言
家里免费送的移动宽带不好用，有时连“中国教育考试网”都上不去 \_\(:3 」∠\)\_ 。可怜“郊区”地方电信连光纤都没有。于是办了个联通的宽带，把接着移动的小米路由器2接到了联通上。移动随便搞了台Tplink接着。  
但是家里两个网实在是不方便，因为小米路由器2算是家庭媒体存储中心，联通又是内网。一旦接到Tplink就访问不到小米路由器了。找遍了小米路由器2的网页管理后台都找不到有关静态路由的设置。便想着开启小米路由器的SSH然后当成Linux来玩。
***
### 准备开搞
首先当然是去“[MiWiFi – 开放平台](http://www.miwifi.com/miwifi_open.html)”（在小米路由器的DNS劫持下要输入 www1.miwifi.com）申请开启SSH，然后下载开启SSH的bin文件。按照页面给出的刷入方式搞进去，等路由器重启以后就能用页面给的root密码登录进去了。  
接着当然是先改root密码，毕竟8位随机字母加数字的密码你记也记不住。
### 第一次尝试
**小米路由器的LUCI是魔改的！不能改switch配置！**  
根据 Openwrt wiki 以及个人知识储备，我开始修改配置文件
>**vim /etc/config/network**  

将  
>config switch_vlan 'eth0_1'  
&nbsp;&nbsp;&nbsp;&nbsp;option device 'eth0'  
&nbsp;&nbsp;&nbsp;&nbsp;option vlan '1'  
&nbsp;&nbsp;&nbsp;&nbsp;option ports '0 2 3 5*'
> 
>config switch_vlan 'eth0_2'  
&nbsp;&nbsp;&nbsp;&nbsp;option device 'eth0'  
&nbsp;&nbsp;&nbsp;&nbsp;option vlan '2'  
&nbsp;&nbsp;&nbsp;&nbsp;option ports '4 5'  

改成了  
>config switch_vlan 'eth0_1'  
&nbsp;&nbsp;&nbsp;&nbsp;option device 'eth0'  
&nbsp;&nbsp;&nbsp;&nbsp;option vlan '1'  
&nbsp;&nbsp;&nbsp;&nbsp;option ports '0 2 5*'
>
>config switch_vlan 'eth0_2'  
&nbsp;&nbsp;&nbsp;&nbsp;option device 'eth0'  
&nbsp;&nbsp;&nbsp;&nbsp;option vlan '2'  
&nbsp;&nbsp;&nbsp;&nbsp;option ports '4 5'  
>
>config switch_vlan 'eth0_3'  
&nbsp;&nbsp;&nbsp;&nbsp;option device 'eth0'  
&nbsp;&nbsp;&nbsp;&nbsp;option vlan '3'  
&nbsp;&nbsp;&nbsp;&nbsp;option ports '3 5'  
...
>
>...  
config interface 'link'  
&nbsp;&nbsp;&nbsp;&nbsp;option ifname 'eth0.3'  
&nbsp;&nbsp;&nbsp;&nbsp;option proto 'dhcp'  

然后信心满满的输入**/etc/init.d/network restart**  
接着把自己电脑的网线接到三号口，输入**dmesg**看到
>robo_link_down: applying EEE WAR for 53125 port 3 link-up

嗯应该口是没错了..........嗯？怎么我有IP地址？怎么我还能跟其他电脑通信？  
算了不管这么多，先试试用网线把两台路由器连起来，然后抓包。  
emmmmmmmmmmmmmmmmm，Tplink的DHCP包跑了过来(｀･д･´) 。  
好吧......... 第一次失败！
