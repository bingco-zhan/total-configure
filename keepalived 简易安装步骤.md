下载地址：
------
https://www.keepalived.org/download.html



vim /etc/hosts 
>>>>
127.0.0.1   keepd132

安装依赖：
------
yum -y install kernel-devel* opensel-* popt-devel lrzsz openssl-clients libnl libnl-devel popt openssl* gcc*


配置：
------
>>>>
mkdir /etc/keepalived
cp /usr/local/keepalived/etc/sbin/keepalived /usr/local/sbin/keepalived
cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/keepalived
vim /etc/keepalived/keepalived.conf


最简单的keepalived.conf配置：
------
>>>>>>>>

global_defs {
   router_id keepd132
}

vrrp_script chk_nginx {
    script "etc/keepalived/chk_nginx.sh"
    interval 2
    weight -20
}

vrrp_instance VI_1 {
    interface ens33
    virtual_router_id 132
    mcast_src_ip 192.168.75.132
    nopreempt
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 0000
    }
    track_script {
        chk_nginx
    }
    virtual_ipaddress {
        192.168.75.100
    }
}

<<<<<<<<<<<


健康检查：
------
vim /etc/keepalived/chk_nginx.sh
>>>>>>>>>>>


#!/bin/bash
val=`ps -C nginx -no-header | wc -l`
if [ $val -eq 0 ]; then
  /usr/local/server/nginx/sbin/nginx
  sleep 2
  if [ `ps -C nginx --no-header | wc -l` -eq 0 ]; then
    kellall keepalived
  fi
fi



启动：
------
keepalived


