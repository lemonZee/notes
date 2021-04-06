1.卸载原有的jdk
[root@zx-centos7-13 jdk1.8.0_251]# rpm -qa|grep jdk
copy-jdk-configs-2.2-3.el7.noarch
java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64
java-1.8.0-openjdk-headless-1.8.0.131-11.b12.el7.x86_64
[root@zx-centos7-13 jdk1.8.0_251]# rpm -e --nodeps java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64
[root@zx-centos7-13 jdk1.8.0_251]# rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.131-11.b12.el7.x86_64


2.安装新的jdk
mkdir /usr/local/java
把源码包放进去
tar -zxvf java-xxxxx

3.配置环境变量
vim /etc/profile

在最后加上
export JAVA_HOME=/usr/local/java/jdk1.8.0xxxx
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

4.更新
source /etc/profile