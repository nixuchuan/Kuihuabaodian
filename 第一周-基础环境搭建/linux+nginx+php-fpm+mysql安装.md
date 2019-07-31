个人资料

ID:52183367

常用名：F1JI

联系方式：邮箱849208778@qq.com  

目前职业：安全服务工程师 1年

地区：杭州

熟悉的语言：java，python

自我介绍：阔以日，但是没必要



linux+nginx+php-fpm+mysql安装

linux：centos6.8直接虚拟机镜像安装即可

nginx：因为想手动安装nginx所以首先得安装nginx的依赖包，先安装pcre库，库直接从网上下载

wget http://ftp.pcre.org/pub/pcre/pcre-8.36.tar.gz

tar -xzvf pcre-8.36.tar.gz

./configure

Make && make install

配置的时候出现报错：configure: error: You need a C++ compiler for C++ support.

是没有c++环境 所以先尝试安装c++环境，使用命令

yum -y install gcc-c++

c++环境安装完成后 即可正常的配置安装

接着安装另外的依赖环境zlib-1.2.8.tar.gz，openssl-1.0.1j.tar.gz，

然后安装nginx，安装完成后启动nginx。

/usr/local/nginx/sbin/nginx，查看nginx是否运行

ps aux | grep nginx 可以看到nginx正在运行的进程

![image-20190724145357328](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190724145357328.png)

![image-20190724145748752](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190724145748752.png)

安装完成后直接访问网站，并没有成功安装的界面弹出，初步怀疑是防火墙禁止了访问，先临时关闭防火墙，查看结果

service iptables stop关闭防火墙，

![image-20190724145957764](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190724145957764.png)

nginx安装成功 是防火墙导致了该问题，稍后再设置防火墙安装策略。



php：

首先检查php是否已经安装，

rpm -qa | grep php 发现本机并未安装。因为php安装的依赖包较多 ，所以直接yum安装

yum install php    

然后php -v 查看php版本。我这离安装的版本是5.3.3，然后回头看了一眼需求，是需要安装php-fpm百度一下发现是5.3.3以后的版本就将php-fpm写进了php的核心源码了 但是我刚好安装了个没用的，所以只能先卸载然后再安装新的了。

使用rpm -e删除php -v出来的，注意这一因为存在依赖关系 所以删除有先后顺序

重新安装新版的php，在官网上下载php5.6.31 下载完成后编译安装

mysql：

直接使用yum安装吧

yum -y install mysql-server mysql-devel

mysql

set password for root@localhost = password('123456'); 设置root密码，



安装完成后发现并不能解析php文件，猜测应该是nginx文件配置错误，尝试修改nginx文件配置。

![image-20190724202046889](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190724202046889.png)

发现问题在此处，修改对应的文件路径 重启nginx即可。

可以正常访问phpinfo

![image-20190724202636773](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190724202636773.png)

接下来尝试下用php操作数据库

发现执行sql语句报错

![image-20190725163359908](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190725163359908.png)

php无法识别mysqli_connect()方法 尝试上网查找解决方案

发现是本地安装php的问题，然后将本地的php全部删除 重新安装php-fpm问题随即解决

![image-20190725190908191](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190725190908191.png)

![image-20190725190920984](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190725190920984.png)

防护策略

服务器基线安全

开放端口 ：遵循最小开放原则，关闭不用端口，限制重要端口不对外，例如21 22 137 445 3306 3389 27017

弱口令

中间件安全

