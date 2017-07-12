---
title: Shadowsocks Server Setup Mark
date: 2017-07-12 14:43:33
tags: FuckGreatWall
---



#Shadowsocks的远程服务器搭建的记录

* 服务器搭建  
* shadowsocks的安装  
* shadowsocks的加速配置   
	linux系统文件配置  
	加速器安装，锐速serverSpeeder
 




##服务器搭建
1，需要准备一台可以低延迟的海外主机，推荐使用 [vultr](www.vultr.com) 云主机。  

2，推荐的操作系统为 CenterOS 6.0 X64 或者 Ubuntu14.04 X64 。因为后面加速软件的限制，我们可选的系统有限，可以参考
[speederServer surpport system](https://www.91yun.org/serverspeeder91yun) 选择。


  
     
  


-----
**UBUTNU 14.04 for example all bellow**   

---

##shadowsocks的安装
这里主要介绍ubuntu的安装，其他可以参考下面的参考文档


	1,  install pip
		 apt-get install python-pip  
	2,  install shadowsocks
		 pip install shadowsocks 



我们需要配置一个可用的shaodowsocks配置文件，可以创建 /etc/shadowsocks.json 如下：  

	{
	    "server":"my_server_ip",
	    "server_port":8000,
	    "local_address": "127.0.0.1",
	    "local_port":1080,
	    "password":"mypassword",
	    "timeout":300,
	    "method":"rc4-md5"
	}	


然后调用 ssserver -c /etc/shadowsocks.json -d start 启动服务

具体配置请移步 ssserver相关资料



##shadowsocks的加速配置
###linux的系统配置文件
1，终端执行 ulimit -n 102400
  
2, 配置内核相关参数   在 /etc/sysctl.conf 添加如下配置，然后 sysctl -p 使配置生效
    
	# max open files 
	fs.file-max = 102400
	# max read buffer
	net.core.rmem_max = 67108864
	# max write buffer
	net.core.wmem_max = 67108864
	# default read buffer
	net.core.rmem_default = 65536
	# default write buffer
	net.core.wmem_default = 65536
	# max processor input queue
	net.core.netdev_max_backlog = 4096
	# max backlog
	net.core.somaxconn = 4096
	
	# resist SYN flood attacks
	net.ipv4.tcp_syncookies = 1
	# reuse timewait sockets when safe
	net.ipv4.tcp_tw_reuse = 1
	# turn off fast timewait sockets recycling
	net.ipv4.tcp_tw_recycle = 0
	# short FIN timeout
	net.ipv4.tcp_fin_timeout = 30
	# short keepalive time
	net.ipv4.tcp_keepalive_time = 1200
	# outbound port range
	net.ipv4.ip_local_port_range = 10000 65000
	# max SYN backlog
	net.ipv4.tcp_max_syn_backlog = 4096
	# max timewait sockets held by system simultaneously
	net.ipv4.tcp_max_tw_buckets = 5000
	# turn on TCP Fast Open on both client and server side
	net.ipv4.tcp_fastopen = 3
	# TCP receive buffer
	net.ipv4.tcp_rmem = 4096 87380 67108864
	# TCP write buffer
	net.ipv4.tcp_wmem = 4096 65536 67108864
	# turn on path MTU discovery
	net.ipv4.tcp_mtu_probing = 1
	
	# for high-latency network
	net.ipv4.tcp_congestion_control = hybla
	
	# for low-latency network, use cubic instead
	# net.ipv4.tcp_congestion_control = cubic

###serverSpeeder 锐速 安装配置
以为锐速官方破产，且支持的软件有限，一个 [破解版 Official Bolog](https://www.91yun.org/archives/683)  做了很多的工作。具体的操作如下：  
1，更换可用内核，这里更换为 3.13.0-24-generic   

	a, check your own kernal version
	   uname -r
	b, install new kernal
	  apt-install linux-image-extra-3.13.0-24-generic
	c, remove old kernal
	  dpkg -l | grep linux-image    // list all kernals
	  apt-get purge linux-image-xxxx-  // remove all your kernals ,for example: sudo apt-get purge linux-image-3.13.0-xx-generic linux-image-extra-3.13.0-xx-generic
	d, reboot
		
		
2,安装 serverSpeeder 在确保系统及内核支持以后，作者提供了非常方便的安装脚本 

		wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder.sh && bash serverspeeder.sh


DONE！！

3，卸载 serverSpeeder   

 		chattr -i /serverspeeder/etc/apx* && /serverspeeder/bin/serverSpeeder.sh uninstall -f

DONE！


#总结
重新启动之后，正常的启动流程如下：  

	1，ulimit -n 102400
	2，ssserver -c /etc/shadowsocks.json -d start
	3, service restart serverSpeeder 
	


#附录
[shadowsocks的官方linux配置教程](https://github.com/shadowsocks/shadowsocks/wiki/Optimizing-Shadowsocks)  
[一个非常棒的简洁配置教程](http://wuchong.me/blog/2015/02/02/shadowsocks-install-and-optimize/)  
[serverSpeeder的微博](https://www.91yun.org/archives/683)  
[serverSpeeder's git](https://github.com/91yun/serverspeeder)
[更换内核原始资料](https://zhidao.baidu.com/question/1832470373002701260.html)  
[VULTR](www.vultr.com)



###加速方案
  其他还有不少加速方案，大家可以自行搜索，比如 net-speeder 等 





