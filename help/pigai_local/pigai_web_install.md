# 批改网镜像安装

1. 我们将解压出来的fxphp文件夹mv或者cp到/data/app/下

            # cp -r fxphp /data/app
2. 更改**/data/app/fxphp/apps/local.pigai.org/server_name.ph**
            # vi /data/app/fxphp/apps/local.pigai.org/server_name.php

    下面代码为**server_name.php**文件内容，我们需要将实际信息填入到里面，如果需要打开本地备份提示验证我们就要**define('_school_admin','')**和**define('_school_admin_psw','')**后面单引号中添加用户名和密码
```
<?php
define('_school','重庆邮电大学');
define('_school_title','重庆邮电大学英语作文本地备份');
define('_school_admin','username');#登陆的用户名，如果这个为空，就没跳出认证
define('_school_admin_psw','password');#登陆的密码
?>
```
3. 更改**/data/app/fxphp/apps/local.pigai.org/server_name.php/conf/conf_db.php**相关信息
这里需要注意**dbuser,dbpass,dbname**都要和我们所创建mysql信息一致
            # vi /data/app/fxphp/apps/local.pigai.org/server_name.php/conf/conf_db.php
更改配置信息

    ```
    <?php
    /**
     * 数据库配置文件
     *
     * @author Coeus ydlhero@gmail.com
     */

    $server_name=  defined('_SERVER_NAME') ? _SERVER_NAME : strtolower(trim( $_SERVER['SERVER_NAME'] ));
    $conf_db = array(
                    //'dbhost'      => '192.168.1.21',      //主机地址
                    'dbhost'        => 'localhost', //主机地址
                    'dbuser'        => 'cqupt',             //用户名
                    'dbpass'        => 'cqupttest!',        //密码
                    'dbname'        => 'pigai_cqupt',       //数据库名
                    'dbport'        => '3306',              //端口
                    'dbcache_type'  => 'file',      //缓存类型，可以是file或者memcache
                    'dbcache_dir'   => ROOT_PATH.'/cache/sql/',     //如果是缓存类型是文件，则此项设定有效，设定缓存路径，>默认为项目路径下的/cache/sql/
                    'dbcache_time'  => 30,          //缓存时间，默认为30秒
            );

    ?>
```

4. 更改**cache**和**lc_2012_c**权限

            # chmod 777 –R /data/app/fxphp/apps/local.pigai.org/cache
            # chmod 777 –R /data/app/fxphp/apps/local.pigai.org/template/lc_2012_c

5. 配置mysql，首选我们以mysql root身份登录，默认安装root密码为空，
            # /usr/local/mysql/bin/mysql –u –root
登陆以后我们要建立一个新的mysql用户避免使用root，
            # GRANT ALL ON *.* TO ‘username’@’localhost’ IDENTIFIED BY ‘password’;
**username和password**就是新用户名称和密码这里建立的要与**conf_db.php信息相符**，当看到提示**Query OK**表示成功
使用新建的用户名登陆
            # /usr/local/mysql/bin/mysql –u username –p
然后会提示输入密码。从新登陆以后继续创建数据库和导入数据。
            # create database pigai_yqz; //建议已pigai为前缀
            # user pigai_yqz; //切换到新建的数据库
            # source /root/pigai_yqz.sql; //导入我们准备好的sql文件
可以用**show database;**看一下表结构是否都导入。
            # show databases;
更改/etc/my.cnf 将mysql_data改成tmp共2处
            # vi /etc/my.cnf
更改完以后记得重启mysql
            # /usr/local/mysql/support-files/mysql.server restart

6. 测试环节：浏览器访问http://192.168.216.41/guest.php IP根据实际情况填写如果成功打开一个打分页面即可。
然后测试本地数据访问：http://192.168.216.41:81/如果再server_name.php中添加了用户名和密码这里会弹出验证。打开开到实际的数据表示OK。
收工！

