# Nginx+PHP+Mysql 安装

1. nginx，php，mysql以及安装脚本已经都封装好，先将准备好的tar或者zip上传到服务器，同理Fat32U挂载即可，如果条件允许，建议找到一个PC下载Xshell利用Xshell工具完成以下操作和维护。

2. 这里我们以Xshell为例，Xshell配置好(服务器信息和上传下载的目录)连接到服务器以后我们可以先安装一个插件方便我们上传和下载
            # yum install -y lrzsz
成功后我们便可以使用**rz**上传，下载**sz**功能，我们将所需要的3个包上传到服务器：**fxphp，pigai_soft**,以及**sql**文件，上传一定是**zip**或者**tar.gz**包的形式不支持文件夹。

3. 将上传的包解压
            # tar -zxvf 包名
unzip的包我们需要先安装支持unzip程序
            # yum install -y unzip
            # unzip 包名
4. 执行安装脚本
我们在解压出来的**pigai_soft**中找**pigai_install.sh** 脚本文件，然后执行**pigai_install.sh**执行过程需要点时间
            # sh pigai_install.sh
如果服务器没有安装过**nginx,php,mysql**基本安装都会成功

5. 安装完成后我们可以看看是否都安装到本地
            # ll /usr/local/
可以看到**php,nginx,mysql**三个文件夹，**/usr/local**也是安装的目录需要记住。我们将**Mysql，nginx，php**分别设置为开机启动：
            # echo "/usr/local/mysql/support-files/mysql.server start" >>/etc/rc.local
            # echo "/etc/init.d/nginx restart" >>/etc/rc.local
            # echo "/etc/init.d/php-fpm start" >> /etc/rc.local

这样基本就完成了LNMP的搭建。

