---
Description: owl is 
Keywords:
- Development
- owl
- monitor
Tags:
- simple
- small
Title: owl监控系统安装过程
Topics:
- owl
- monitor
date: 2016-12-25
---

owl是talkingdata公司开源的分布式监控系统

##  架构设计
- agent(plugins) + cfc + mysql(tsdb)


## 特性

- Go语言开发，部署维护简单
- 分布式，支持多机房
- 多维的数据模型，类opentsdb
- 支持多种报警算法，报警支持多条件组合、克隆
- 灵活的插件机制，支持任意语言编写，支持传参
- 丰富的报警渠道，邮件、微信、短信
- 原始数据永久存储
- 自带web管理界面以及强大的自定义图表功能

## Demo

http://54.223.127.87/

demo/demo

## 安装过程
### (1/5). install hbase  
wget -c -t 0 http://www.apache.org/dist/hbase/stable/hbase-1.2.4-bin.tar.gz  
tar zxvf hbase-1.2.4-bin.tar.gz  
cd hbase-1.2.4  
nano hbase-env.sh 增加 JAVA_HOME变量  

```
cat > ./conf/hbase-site.xml << EOF
<configuration>
<property>
<name>hbase.rootidr</name>
<value>file:///home/oslet/soft/hbase-1.2.4</value>
</property>
<property>
<name>hbase.zookeeper.property.dataDir</name>
<value>/home/oslet/soft/hbase-1.2.4/zookeeper</value>
</property>
</configuration>
EOF
```

./bin/start-hbase.sh  

### (2/5). install opentsdb
git clone git://github.com/OpenTSDB/opentsdb.git  
cd opentsdb  
./build.sh  

```
cat > ./build/opentsdb.conf <<EOF
tsd.network.port = 4242
tsd.http.staticroot = /home/oslet/soft/opentsdb/build/staticroot
tsd.http.cachedir = /tmp/opentsdb
tsd.core.auto_create_metrics = true
tsd.core.plugin_path= /home/oslet/soft/opentsdb/build/plugins
tsd.storage.hbase.zk_quorum=127.0.0.1:2181
EOF
```

```
cat > start.sh <<EOF
#!/bin/bash


#sudo apt-get install gnuplot
#sudo apt-get install autoconf
#sudo apt-get install git
#git clone git://github.com/OpenTSDB/opentsdb.git

#cd opentsdb
#./build.sh

#--------------------------------------------------------

#env COMPRESSION=NONE HBASE_HOME=/home/oslet/soft/hbase-1.2.4 `pwd`/src/create_table.sh

#--------------------------------------------------------

#tsdtmp=${TMPDIR-'/tmp'}/tsd
#master_ip=127.0.0.1 
#mkdir -p "$tsdtmp"
#./build/tsdb tsd --port=4242 --staticroot=build/staticroot --cachedir="$tsdtmp" --zkquorum=master_ip:2181,slave1_ip:2181,slave2_ip:2181
./build/tsdb tsd --config ./build/opentsdb.conf
EOF
```

chmod +x ./start.sh  
./start.sh  

### (3/5). Install mysql  
sudo apt install mysql-server  
service mysql start  

### (4/5). Install owl  
cd $GOPATH/src  
git clone https://github.com/TalkingData/owl  
cd owl  
godep go install owl/cfc  
godep go install owl/controller  
godep go install owl/inspector  
godep go install owl/netcollect  
godep go install owl/repeater  
godep go install owl/proxy  
godep go install owl/api  
godep go install owl/agent  

mkdir bin  
```
cp -a $GOPATH/bin/cfc \
$GOPATH/bin/controller \
$GOPATH/bin/inspector \
$GOPATH/bin/netcollect \
$GOPATH/bin/repeater \
$GOPATH/bin/proxy \
$GOPATH/bin/api \
$GOPATH/bin/agent \
bin
```
cp -a conf bin  
依次修改conf目录中的配置文件为正确的信息，即可启动了  
在scripts/init目录中有自动启动脚本，修改一下相关路径  
也可加入开机启动, 临时启动如下  
./cfc &  
./controller &  
./inspector &  
./netcollect &  
./repeater &  
./proxy &  
./api &  
./agent &  

web展示程序是在 static 目录,需安装nginx  
sudo apt install nginx  

```
cat > /etc/nginx/sites-available/default <<EOF
server {
	server_name localhost;
	listen 80;
	
location /api {
	proxy_pass http://127.0.0.1:10060;
	proxy_set_header Connection "";
	proxy_set_header Host $host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	}

location / {
	root	/opt/owl/static;
	index	index_prod.html;
    }
}
EOF
```

需修改 index_prod.html 中 api 为正确的线上地址.  

### (5/5). 手动插入一条用户信息到 user表  
insert into user(username,password,role,status) value ('abc','64fa4cc59b3358fc0649b0a34e9f1b13','1','1')  

注意password字段是md5加密串,与命令行md5sum生成的有区别,可用如下代码生成  
```
package main

import (
	"crypto/md5"
	"encoding/hex"
	"fmt"
)

func Md5(c string) string {
	if len(c) == 0 {
		return ""
	}
	hash := md5.New()
	hash.Write([]byte(c))
	return hex.EncodeToString(hash.Sum(nil))
}

func main() {
	var a string = "eletmc"
	fmt.Println("pass is : ", Md5(a))
}
```
