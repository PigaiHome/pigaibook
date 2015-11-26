# Linux系统安装与配置

这里以**Redhat6.4**和**CentOS6.7Mini**为例（文字较多不用怕，只是详细而已）

**一、Linux安装**

1、将所需要的Linux系统制作U盘或者光盘启动盘

2、U盘或者光盘启动后回到一个“欢迎界面”，不考虑RAID卡驱动情况下默认选择第一个“**install or up grade an existing system **”

3、出现**Disc Found**界面，用TAB切换到SKIP回车跳过。

4、安装主界面直接点击“NEXT”。

5、给系统选择语言，根据个人爱好。English或者简体中文继续“Next”。

6、选择默认键盘“美国英语式”继续“下一步”。

7、选择基本存储设备，默认第一个点击“下一步”。

8、存储设备警告，因为我们基本都是全新的硬盘所以选择“是，忽略所有数据”点击“下一步”。

9、定义计算机名称，这里大家可以在下方看到配置网络，个人比较喜欢命令行下操作，根据自身情况定。点击“下一步”。

10、选择时区，这里注意如果前面系统语言选择的是English那这里需要更改时区：亚洲上海。点击“下一步”。

11、设置超级管理员**ROOT**密码，可以忽略密码设计策略直接选择“无论如何都使用”，点击“下一步”。

12、安装种类选择，因为是全新的硬盘我们选择第一个“使用所有空间”，点击“下一步”。

13、这时会出现让我们选择安装系统的硬盘，物理环境下，基本都会有多块或者**RAID**，这里一定要注意选择好我们要安装系统的盘符，选择向右的箭头添加到右侧区域然后选中。点击“下一步”。

14、会弹出一个对话框“将存储配置写入磁盘”选择“将修改写入磁盘”。点击“下一步”。

15、环境组件选择，默认“基本服务器”即可，也可以将桌面组件选中。（**CentOS6.7mini**不提供组件选择），点击“下一步”。

16、进入到最后的安装进度界面，安装过程完成后下一步，最后选择重新引导，重新引导后我们会进入到输入用户名面的黑色白字界面，输入我们刚才设置的root信息即可。


**二、服务器配置**

**1、网卡配置：**

**root**登陆以后输入：
            # vi /etc/sysconfig/network-scripts/ifcfg-eth0
eth0代表第一块网卡，如果遇到多网卡情况请检查网线所对应的网卡，
检测方法：
            # ethtool -p eth0
**eth0**对应的网卡会闪烁。（注意要在不插网线的时候进行）
**eth0**是网卡名字，具体名字需要到**/etc/sysconfig/network-scripts/**下查看
打开配置文件后我们需要更改的参数：
            # IPADDR=x.x.x.x    //服务器实际的IP地址
            # NETMASK=x.x.x.x   //子网掩码
            # GATEWAY=x.x.x.x   //服务器实际制定的网关
            # DNS1,DNS2         //可以根据实际情况来定建议使用谷歌的8.8.8.8。
            # BBOTPROTO=static  //改成static表示静态
            # ONBOOT=yes        //启用网卡

设置完网卡后我们**:wq**保存退出，重启一下服务：
            # service network restart
正常应该对会显示OK或确定，然后我们可以**ping**下内网以及外网，测试下。

**2、防火墙配置：**
            # vi /etc/sysconfig/iptables
保证：**22，80，81**端口开放
            # -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
            # -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
            # -A INPUT -m state --state NEW -m tcp -p tcp --dport 81 -j ACCEPT

保存退出重新启动防火墙服务：
            # service iptables restart

**重点：Redhat默认需要注册才能使用yum源，所以这里需要更改yum源（如果是CentOS这步可以忽略）**

1. 将原有的yum删除：
            # rpm -qa|grep yum|xargs rpm -e -nodeps

2. 准备**yum**源相关安装文件4个，建议先把相关文件准备好，然后挂载**Fat32 U**盘形式**copy**到服务器，也可以使用**wget**得到，还有需要注意的是不一定4个组件和我给出的完全一摸样主要是前面相同，小版本号可以不同：
            yum-3.2.27-14.el6.centos.noarch.rpm
            yum-metadata-parser-1.1.2-14.1.el6.x86_64.rpm
            yum-plugin-fastestmirror-1.1.26-11.el6.noarch.rpm
            python-iniparse-0.3.1-2.1.el6.noarch.rpm
以下为**wget**方法，直接复制到控制台即可（如果出问题需要自行找到能下载的链接）：
            # wget http://ftp.sjtu.edu.cn/centos/6/os/x86_64/Packages/yum-3.2.29-69.el6.centos.noarch.rpm
            # wget http://ftp.sjtu.edu.cn/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
            # wget http://ftp.sjtu.edu.cn/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-30.el6.noarch.rpm
            # wget http://ftp.sjtu.edu.cn/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm

3.	安装刚下好的4个rpm包：
            # rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
            # rpm -ivh yum-metadata-parser-1.1.2-14.1.el6.x86_64.rpm
            # rpm -ivh yum-3.2.27-14.el6.centos.noarch.rpm yum-plugin-fastestmirror-1.1.26-11.el6.noarch.rpm （注意这2个包要一起安装有连带关系）

4.  替换yum源：我们先将**/etc/yum.repos.d/rhel.source.repo**做个备份,然后将它删除并用vi建立一个新的 **Centos-Base.repo**，将下面分割线内信息拷贝到新建的文件里，保存退出。
            # cp /etc/yum.repos.d/rhel.source.repo.bak
            # rm -f /etc/yum.repos.d/rhel.source.repo
            # vi /etc/yum.repos.d/Centos-Base.repo
将下面的信息copy到新建立的**Centos-Base.repo**

```shell
    # CentOS-Base.repo
    #
    # The mirror system uses the connecting IP address of the client and the
    # update status of each mirror to pick mirrors that are updated to and
    # geographically close to the client.  You should use this for CentOS updates
    # unless you are manually picking other mirrors.
    #
    # If the mirrorlist= does not work for you, as a fall back you can try the
    # remarked out baseurl= line instead.
    #
    #

    [base]
    name=CentOS-6 - Base
    mirrorlist=http://mirrorlist.centos.org/?release=6&arch=$basearch&repo=os&infra=$infra
    #baseurl=http://mirror.centos.org/centos/6/os/$basearch/
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    #released updates
    [updates]
    name=CentOS-6 - Updates
    mirrorlist=http://mirrorlist.centos.org/?release=6&arch=$basearch&repo=updates&infra=$infra
    #baseurl=http://mirror.centos.org/centos/6/updates/$basearch/
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    #additional packages that may be useful
    [extras]
    name=CentOS-6 - Extras
    mirrorlist=http://mirrorlist.centos.org/?release=6&arch=$basearch&repo=extras&infra=$infra
    #baseurl=http://mirror.centos.org/centos/6/extras/$basearch/
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    #additional packages that extend functionality of existing packages
    [centosplus]
    name=CentOS-6 - Plus
    mirrorlist=http://mirrorlist.centos.org/?release=6&arch=$basearch&repo=centosplus&infra=$infra
    #baseurl=http://mirror.centos.org/centos/6/centosplus/$basearch/
    gpgcheck=1
    enabled=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    #contrib - packages by Centos Users
    [contrib]
    name=CentOS-6 - Contrib
    mirrorlist=http://mirrorlist.centos.org/?release=6&arch=$basearch&repo=contrib&infra=$infra
    #baseurl=http://mirror.centos.org/centos/6/contrib/$basearch/
    gpgcheck=1
    enabled=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

```

然后执行：
```shell
   yum clean all
   yum makecache
```
正常情况会出现下载的进度条一直到结束然后我们测试下
            # yum install -y unzip
出现下载安装的进度条表明我们yun源配置成功。**yum**源有不少也可以根据自己的喜好选择。

**pigai_install.sh 代码**

```shell
把shell代码放这里
```
