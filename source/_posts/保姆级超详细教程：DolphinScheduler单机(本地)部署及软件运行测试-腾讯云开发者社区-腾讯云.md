---
title: DolphinScheduler单机(本地)部署
---

# 保姆级超详细教程：DolphinScheduler单机(本地)部署及软件运行测试
![image.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/29792214/1715239258850-615f3af1-7103-47dd-92c6-74067e8dca43.jpeg#clientId=u846a0b5a-ddc0-4&from=paste&id=u57393bdb&originHeight=200&originWidth=200&originalType=url&ratio=1&rotation=0&showTitle=false&size=5287&status=done&style=none&taskId=u6c0dc3ae-8c6f-4503-831f-1621e9b1084&title=)
大数据真好玩
关注
发布于 2021-12-02 10:11:44
4.2K0
发布于 2021-12-02 10:11:44
举报
文章被收录于专栏：[暴走大数据](https://cloud.tencent.com/developer/column/79529)[暴走大数据](https://cloud.tencent.com/developer/column/79529)
### **DolphinScheduler架构图**
![image.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/29792214/1715239258918-b1f2635e-81a4-4da8-ab9e-9f84b31f630c.jpeg#clientId=u846a0b5a-ddc0-4&from=paste&id=u1ab9d106&originalType=url&ratio=1&rotation=0&showTitle=false&size=51276&status=done&style=none&taskId=u56d5d3b1-315b-4fd3-bda8-da8cc77a63d&title=)
##### **目录**
一、补充
二、部署流程
1.下载二进制tar.gz包
2.创建部署用户并赋予目录操作权限
3.ssh免密配置
4.[数据库](https://cloud.tencent.com/solution/database?from_column=20065&from=20065)初始化
5.修改运行参数
6.一键部署
7.登录系统
如果你还不够熟悉DolphinScheduler，可以参考：

- [《调度系统Apache DolphinScheduler介绍和设计原理》](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494139%26idx%3D2%26sn%3D1af1d42dce6fdcd3bbe3382fca653b23%26chksm%3De9bff8d7dec871c1ddebcc37a72bd6ca41ea908332fae08bc8f57437894d31dc77e9358de532%26token%3D422302214%26lang%3Dzh_CN%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)
- [《DolphinScheduler分布式作业管理平台》](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247486115%26idx%3D2%26sn%3D0823d445c5f2e6079de68277a3f6d60a%26chksm%3De9bc1b8fdecb929933ac403f92414ad1bed8d00145b09b5441b44bf3ea0f788e288b9e2013e8%26token%3D422302214%26lang%3Dzh_CN%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)
### [一、补充](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494907%26idx%3D1%26sn%3D4923ca218bdbb60f12819592e7fea6f7%26chksm%3De9bffdd7dec874c1b354060085818929279099287563dd1a8d3b4758e8a24793ef0a5a3444b3%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)
安装psmisc：
代码语言：javascript
复制
```javascript
apt-get install psmisc
```
### [二、部署流程](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494890%26idx%3D2%26sn%3D788a90a7cfaa4f2698cad0e5445e3608%26chksm%3De9bffdc6dec874d04d4809c8dae3e67dba1e107a60ea470ca85f7edc3619ff16a1f44b5423ad%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)
#### **1.下载二进制tar.gz包**
下载最新版本的后端安装包至[服务器](https://cloud.tencent.com/act/pro/promotion-cvm?from_column=20065&from=20065)部署目录，比如创建 /opt/dolphinscheduler 做为安装部署目录，下载地址：https://dlcdn.apache.org/dolphinscheduler/1.3.8/apache-dolphinscheduler-1.3.8-src.tar.gz，下载后上传 tar 包到该目录中
注：此处应当下载bin文件
![image.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/29792214/1715239258789-462e2eed-b51e-4aa2-9950-61c991e8ac2c.jpeg#clientId=u846a0b5a-ddc0-4&from=paste&id=u472233b5&originalType=url&ratio=1&rotation=0&showTitle=false&size=55934&status=done&style=none&taskId=u0c85523b-36e4-478a-9fa6-41fad3e77aa&title=)
解压
代码语言：javascript
复制
```javascript
# 创建部署目录，部署目录请不要创建在 /root、/home 等高权限目录 
mkdir -p /opt/dolphinscheduler
cd /opt/dolphinscheduler

# 解压缩
tar -zxvf apache-dolphinscheduler-1.3.8-bin.tar.gz
 
#重命名
mv apache-dolphinscheduler-1.3.8-bin  dolphinscheduler-bin
```
笔者在/usr/local位置上创建dolphinscheduler文件夹，上传并解压了安装包。随后将压缩包重命名为dolphinscheduler-bin。
代码语言：javascript
复制
```javascript
mkdir dolphinscheduler
cd dolphinscheduler

# 解压缩
tar -zxvf apache-dolphinscheduler-1.3.8-bin.tar.gz -C
 
#重命名
mv apache-dolphinscheduler-1.3.8-bin  dolphinscheduler-bin
```
附：
查看虚拟机ip地址
代码语言：javascript
复制
```javascript
ifconfig
```
inet后跟的值即为ip地址
随后用filezilla连接虚拟机，传输文件
![image.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/29792214/1715239258936-e4b93fc3-1050-43aa-8532-fdac87e87caf.jpeg#clientId=u846a0b5a-ddc0-4&from=paste&id=u0d9b9890&originHeight=610&originWidth=874&originalType=url&ratio=1&rotation=0&showTitle=false&size=51947&status=done&style=none&taskId=u246d7497-1c8e-4967-9988-0e163931f58&title=)
主机号即为刚刚查询的结果，协议选择SFTP而不是默认的FTP，用户名为虚拟机系统的用户名而不是linux终端terminal的用户。
连接成功后即可看到linux文件目录，用拖拽的方式即可传输文件。
笔者此处遇到权限问题：
filezilla报错：
代码语言：javascript
复制
```javascript
命令:    put "C:\Users\86136\Desktop\apache-dolphinscheduler-1.3.8-src.tar.gz" "apache-dolphinscheduler-1.3.8-src.tar.gz"
错误: /usr/local/dolphinscheduler/apache-dolphinscheduler-1.3.8-src.tar.gz: open for write: permission denied
错误:    文件传输失败
```
在虚拟机上授权
代码语言：javascript
复制
```javascript
sudo chmod 777 /usr/local/dolphinscheduler
```
重新传输，问题解决。
实际上，在安装filezilla后直接拖拽文件进虚拟机界面也能实现文件传输，但就笔者而言这一操作偶尔能够成功但常常导致系统死机。
#### [2.创建部署用户并赋予目录操作权限](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494890%26idx%3D2%26sn%3D788a90a7cfaa4f2698cad0e5445e3608%26chksm%3De9bffdc6dec874d04d4809c8dae3e67dba1e107a60ea470ca85f7edc3619ff16a1f44b5423ad%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)

- 创建部署用户，并且一定要配置 sudo 免密。以创建 dolphinscheduler 用户为例

代码语言：javascript
复制
```javascript
# 创建用户需使用 root 登录
useradd dolphinscheduler

# 添加密码
echo "dolphinscheduler" | passwd --stdin dolphinscheduler

# 配置 sudo 免密
sed -i '$adolphinscheduler  ALL=(ALL)  NOPASSWD: NOPASSWD: ALL' /etc/sudoers
sed -i 's/Defaults    requirett/#Defaults    requirett/g' /etc/sudoers

# 修改目录权限，使得部署用户对 dolphinscheduler-bin 目录有操作权限
chown -R dolphinscheduler:dolphinscheduler dolphinscheduler-bin
```
注意：

- 因为任务执行服务是以 sudo -u {linux-user} 切换不同 linux 用户的方式来实现多租户运行作业，所以部署用户需要有 sudo 权限，而且是免密的。初学习者不理解的话，完全可以暂时忽略这一点
- 如果发现 /etc/sudoers 文件中有 "Defaults requirett" 这行，也请注释掉
- 如果用到资源上传的话，还需要给该部署用户分配操作本地文件系统或者 HDFS 或者 MinIO的权限

坑：
代码语言：javascript
复制
```javascript
echo "dolphinscheduler" | passwd --stdin dolphinscheduler
```
这步会报错。linux的passwd命令没有--stdin的语法
应该用
代码语言：javascript
复制
```javascript
echo "dolphinscheduler:123456" | chpasswd
```
修改用户dolphinscheduler密码为123456
另外这五条指令在第一次成功运行时都没有运行成功提示。
#### [3.ssh免密配置](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494755%26idx%3D3%26sn%3D2e407bda03e3a392387a1f276f554927%26chksm%3De9bffd4fdec8745963dc186d565a7077e1dc7e4d05e3099f105aaec5111ef013a6dfe1779376%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)

- 切换到部署用户并配置 ssh 本机免密登录

代码语言：javascript
复制
```javascript
su dolphinscheduler

ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```
注意：正常设置后，dolphinscheduler 用户在执行命令 ssh localhost 是不需要再输入密码的
坑：这里运行会报错Could not create directory '/home/dolphinscheduler/.ssh
需要先以root身份在/home下创建dolphinscheduler目录再在dolphinscheduler里创建.ssh目录，
然后切换回dolphinscheduler用户执行。除此之外可能还需要对这些目录进行授权。
#### [4.数据库初始化](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494354%26idx%3D1%26sn%3D50123991ffe4e806bd18dc393e28efb5%26chksm%3De9bffbfedec872e87451bbacab6e7586452d42b3ae7f0b2789f12437e9dcf37c877867711e11%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)

- 进入数据库，默认数据库是 [PostgreSQL](https://cloud.tencent.com/product/postgresql?from_column=20065&from=20065)，如选择 MySQL 的话，后续需要添加 mysql-connector-java 驱动包到 DolphinScheduler 的 lib 目录下

mysql-connector-java下载网址：MySQL :: Download Connector/J
注意切换下载linux版并且注意对应版本号。
这里对应的主要是ubuntu的版本而不是linux版本。
![image.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/29792214/1715239258889-cfd98bfb-3ebf-4196-b269-a46682624e62.jpeg#clientId=u846a0b5a-ddc0-4&from=paste&id=u9c242e8a&originHeight=422&originWidth=953&originalType=url&ratio=1&rotation=0&showTitle=false&size=41683&status=done&style=none&taskId=u49421081-b40b-41c4-a6dd-d8fc2061863&title=)
查看ubuntu版本的方法 ：
代码语言：javascript
复制
```javascript
cat /proc/version
```
结果如下：
![image.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/29792214/1715239282851-e63ac640-8f95-45f7-ae7c-9e32e5218c39.jpeg#clientId=u846a0b5a-ddc0-4&from=paste&id=u56bd7de0&originHeight=34&originWidth=735&originalType=url&ratio=1&rotation=0&showTitle=false&size=10423&status=done&style=none&taskId=udbacdb88-6409-430d-a919-f015ccbf10f&title=)
笔者用的18.04版本
下载好后放到DolphinScheduler 的 lib 目录下解压
但是笔者此处遇到一点问题，下载下来的文件解压后的结果似乎并不太常规，没法简单地tar解包
解决方法是在windows中解压，然后打开解压出的压缩包，该压缩包里./usr/share/java里有我们需要的mysql-connector-java-8.0.26.jar，取出来传入linux的lib文件夹下。
之后执行如下指令进入数据库：
代码语言：javascript
复制
```javascript
#启动mysql服务
service mysql start

#登录
mysql -uroot -p
```

- 此处遇到了另一个问题，运行service mysql start报错Failed to start mysql.service: Unit mysql.service not found.
- 解决方法：

代码语言：javascript
复制
```javascript
#查询/etc/init.d/下是否存在mysql，无结果说明不存在
ll /etc/init.d/ | grep mysql

#查询mysql.server所在位置
find / -name mysql.server

#把mysql.server复制过去，其中/usr/local/mysql/是笔者的mysql安装目录
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

- 进入数据库命令行窗口后，执行数据库初始化命令，设置访问账号和密码。注: {user} 和 {password} 需要替换为具体的数据库用户名和密码

代码语言：javascript
复制
```javascript
mysql> CREATE DATABASE dolphinscheduler DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
   mysql> GRANT ALL PRIVILEGES ON dolphinscheduler.* TO '{user}'@'%' IDENTIFIED BY '{password}';
   mysql> GRANT ALL PRIVILEGES ON dolphinscheduler.* TO '{user}'@'localhost' IDENTIFIED BY '{password}';
   mysql> flush privileges;
```
注：如果需要查看mysql中的用户及其密码可使用如下代码
代码语言：javascript
复制
```javascript
SELECT User, Host, Password FROM mysql.user;
```
创建表和导入基础数据
首先退出sql(按Ctrl+c)
接下来修改 conf 目录下 datasource.properties 中的下列配置
代码语言：javascript
复制
```javascript
cd /usr/local/dolphinscheduler/dolphinscheduler-bin

vi conf/datasource.properties
```

- 如果选择 MySQL，请注释(用‘#’)掉 PostgreSQL 相关配置(反之同理)，还需要手动添加 [ mysql-connector-java 驱动 jar ] 包到 lib 目录下，这里下载的是 mysql-connector-java-5.1.47.jar，然后正确配置数据库连接相关信息
- 提示：切换至英文输入法，输入i进入修改状态，方向键改变光标位置，完成后先按Esc退出插入状态，再输入:wq保存并退出。:q可用来直接退出而不保存。

代码语言：javascript
复制
```javascript
# postgre
  # spring.datasource.driver-class-name=org.postgresql.Driver
  # spring.datasource.url=jdbc:postgresql://localhost:5432/dolphinscheduler
  # mysql
  spring.datasource.driver-class-name=com.mysql.jdbc.Driver
  spring.datasource.url=jdbc:mysql://xxx:3306/dolphinscheduler?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true     # 需要修改ip，本机localhost即可
  spring.datasource.username=xxx      # 需要修改为上面的{user}值
  spring.datasource.password=xxx      # 需要修改为上面的{password}值
```

- 先把PostgreSQL 相关的代码注释掉，然后注意修改这里的xxx
- 修改并保存完后，执行 script 目录下的创建表及导入基础数据脚本

代码语言：javascript
复制
```javascript
sh script/create-dolphinscheduler.sh
```
注意: 如果执行上述脚本提示 “/bin/java: No such file or directory” 错误，请在 /etc/profile 下配置 JAVA_HOME 及 PATH 变量
正好笔者此处也遇到了类似的问题：
script/create-dolphinscheduler.sh: 37: script/create-dolphinscheduler.sh: /bin/java: not found
那么我们就按照提示进入/etc文件夹下的profile文件进行编辑吧：
代码语言：javascript
复制
```javascript
vi /etc/profile
```
在最下方添加如下代码：
代码语言：javascript
复制
```javascript
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
JRE_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre
PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
```
> 当然此处可能由于每个人jdk所在位置以及版本不同而需要做出适当修改。 如果之前使用的npm安装jdk，那么jdk很有可能被安装在/usr/lib/jvm目录下 而我们所需的java文件地址为：/usr/lib/jvm/java-8-openjdk-amd64/bin/java （此处可能略有不同）

:wq保存退出。
之后再敲入如下代码：
代码语言：javascript
复制
```javascript
source /etc/profile   //使修改立即生效
echo $JAVA_HOME //查看JAVA_HOME的值
javac -version
```
第一条使设置生效，二三条检验配置是否成功。
#### **5.修改运行参数**

- 修改 /usr/local/dolphinscheduler/dolphinscheduler-bin/conf/env 目录下的 dolphinscheduler_env.sh 环境变量(以相关用到的软件都安装在 /opt/soft 下为例)

代码语言：javascript
复制
```javascript
vi /usr/local/dolphinscheduler/dolphinscheduler-bin/conf/env/dolphinscheduler_env.sh
```
注释export SPARK_HOME1=/opt/soft/spark1这行
export DATAX_HOME=/opt/soft/datax/bin/datax这行加上.py
代码语言：javascript
复制
```javascript
export HADOOP_HOME=/opt/soft/hadoop
export HADOOP_CONF_DIR=/opt/soft/hadoop/etc/hadoop
# export SPARK_HOME1=/opt/soft/spark1
export SPARK_HOME2=/opt/soft/spark2
export PYTHON_HOME=/opt/soft/python
export JAVA_HOME=/opt/soft/java
export HIVE_HOME=/opt/soft/hive
export FLINK_HOME=/opt/soft/flink
export DATAX_HOME=/opt/soft/datax/bin/datax.py
export PATH=$HADOOP_HOME/bin:$SPARK_HOME2/bin:$PYTHON_HOME:$JAVA_HOME/bin:$HIVE_HOME/bin:$FLINK_HOME/bin:$DATAX_HOME:$PATH
```
注意: 这一步非常重要，例如 JAVA_HOME 和 PATH 是必须要配置的，没有用到的可以忽略或者注释掉；如果找不到 dolphinscheduler_env.sh，请运行 ls -a

- 将 jdk 软链到 /usr/bin/java 下(仍以 JAVA_HOME=/opt/soft/java 为例)

代码语言：javascript
复制
```javascript
sudo ln -s /opt/soft/java/bin/java /usr/bin/java
```

- 修改一键部署配置文件conf/config/install_config.conf中的各参数，特别注意以下参数的配置

代码语言：javascript
复制
```javascript
vim /usr/local/dolphinscheduler/dolphinscheduler-bin/conf/config/install_config.conf
```
代码语言：javascript
复制
```javascript
# 这里填 mysql or postgresql
dbtype="mysql"

# 数据库连接地址
dbhost="localhost:3306"

# 数据库名
dbname="dolphinscheduler"

# 数据库用户名，此处需要修改为上面设置的 {user} 具体值
username="xxx"    

# 数据库密码，如果有特殊字符，请使用 \ 转义，需要修改为上面设置的 {password} 具体值
password="xxx"

# Zookeeper地址，单机本机是 localhost:2181，记得把 2181 端口带上
zkQuorum="localhost:2181"

# 将 DS 安装到哪个目录，如: /opt/soft/dolphinscheduler，不同于现在的目录
installPath="/opt/soft/dolphinscheduler"

# 使用哪个用户部署，使用第 3 节创建的用户
deployUser="dolphinscheduler"

# 邮件配置，以 qq 邮箱为例
# 邮件协议
mailProtocol="SMTP"

# 邮件服务地址
mailServerHost="smtp.qq.com"

# 邮件服务端口
mailServerPort="25"

# mailSender 和 mailUser 配置成一样即可
# 发送者
mailSender="xxx@qq.com"

# 发送用户
mailUser="xxx@qq.com"

# 邮箱密码
mailPassword="xxx"

# TLS 协议的邮箱设置为 true，否则设置为 false
starttlsEnable="true"

# 开启 SSL 协议的邮箱配置为 true，否则为 false。注意: starttlsEnable 和 sslEnable 不能同时为 true
sslEnable="false"

# 邮件服务地址值，参考上面 mailServerHost
sslTrust="smtp.qq.com"

# 业务用到的比如 sql 等资源文件上传到哪里，可以设置：HDFS,S3,NONE，单机如果想使用本地文件系统，请配置为 HDFS，因为 HDFS 支持本地文件系统；如果不需要资源上传功能请选择 NONE。强调一点：使用本地文件系统不需要部署 hadoop
resourceStorageType="HDFS"

# 这里以保存到本地文件系统为例
# 注：但是如果你想上传到 HDFS 的话，NameNode 启用了 HA，则需要将 hadoop 的配置文件 core-site.xml 和 hdfs-site.xml 放到 conf 目录下，本例即是放到 /opt/dolphinscheduler/conf 下面，并配置 namenode cluster 名称；如果 NameNode 不是 HA，则修改为具体的 ip 或者主机名即可
defaultFS="file:///data/dolphinscheduler"    #hdfs://{具体的ip/主机名}:8020

# 如果没有使用到 Yarn，保持以下默认值即可；如果 ResourceManager 是 HA，则配置为 ResourceManager 节点的主备 ip 或者 hostname，比如 "192.168.xx.xx,192.168.xx.xx" ;如果是单 ResourceManager 请配置 yarnHaIps="" 即可
# 注：依赖于yarn执行的任务，为了保证执行结果判断成功，需要确保yarn信息配置正确
yarnHaIps="192.168.xx.xx,192.168.xx.xx"

# 如果 ResourceManager 是 HA 或者没有使用到 Yarn 保持默认值即可；如果是单 ResourceManager，请配置真实的 ResourceManager 主机名或者 ip
singleYarnIp="yarnIp1"

# 资源上传根路径，支持 HDFS 和 S3，由于 hdfs 支持本地文件系统，需要确保本地文件夹存在且有读写权限
resourceUploadPath="/data/dolphinscheduler"

# 具备权限创建 resourceUploadPath的用户
hdfsRootUser="hdfs"
    
# 配置 api server port
apiServerPort="12345"

# 在哪些机器上部署 DS 服务，本机选 localhost
ips="localhost"

# ssh端口，默认22
sshPort="22"

# master服务部署在哪台机器上
masters="localhost"

# worker服务部署在哪台机器上，并指定此 worker 属于哪一个 worker 组，下面示例的 default 即为组名
workers="localhost:default"

# 报警服务部署在哪台机器上
alertServer="localhost"

# 后端api服务部署在在哪台机器上
apiServers="localhost"
```
注：如果打算用到 资源中心 功能，请执行以下命令：
代码语言：javascript
复制
```javascript
sudo mkdir /data/dolphinscheduler
sudo chown -R dolphinscheduler:dolphinscheduler /data/dolphinscheduler
```
#### **6.一键部署**

- 切换到部署用户，执行一键部署脚本

代码语言：javascript
复制
```javascript
sh install.sh
```
注意：第一次部署的话，在运行中第3步 3,stop server 出现 5 次以下信息，此信息可以忽略
代码语言：javascript
复制
```javascript
sh: bin/dolphinscheduler-daemon.sh: No such file or directory
```

- 运行时报错：install.sh: 22: install.sh: source: not found 1.replace file install.sh: 28: install.sh: [[: not found install.sh: 34: [: ==: unexpected operator 2.create directory 3.scp resources /usr/local/dolphinscheduler/dolphinscheduler-bin/script/scp-hosts.sh: 21: /usr/local/dolphinscheduler/dolphinscheduler-bin/script/scp-hosts.sh: source: not found /usr/local/dolphinscheduler/dolphinscheduler-bin/script/scp-hosts.sh: 24: /usr/local/dolphinscheduler/dolphinscheduler-bin/script/scp-hosts.sh: [[: not found /usr/local/dolphinscheduler/dolphinscheduler-bin/script/scp-hosts.sh: 29: /usr/local/dolphinscheduler/dolphinscheduler-bin/script/scp-hosts.sh: Syntax error: "(" unexpected scp copy failed to exit

原因：默认使用dash作为shell，但sh是bash shell的命令。
解决方案：
代码语言：javascript
复制
```javascript
dpkg-reconfigure dash（需要root权限）
在界面中选择no
再运行ls -l /bin/sh 后显示/bin/sh -> bash
```

- 再运行sh命令即可
- 脚本完成后，会启动以下 5 个服务，使用

代码语言：javascript
复制
```javascript
jps
```
命令查看服务是否启动( jps 为 JDK 自带)

- 如果显示中包含：

代码语言：javascript
复制
```javascript
MasterServer         ----- master服务
    WorkerServer         ----- worker服务
    LoggerServer         ----- logger服务
    ApiApplicationServer ----- api服务
    AlertServer          ----- alert服务
```
如果以上服务都正常启动，说明自动部署成功
部署成功后，可以进行日志查看，日志统一存放于 logs 文件夹内
/usr/dolphinscheduler/logs
代码语言：javascript
复制
```javascript
logs/
    ├── dolphinscheduler-alert-server.log
    ├── dolphinscheduler-master-server.log
    |—— dolphinscheduler-worker-server.log
    |—— dolphinscheduler-api-server.log
    |—— dolphinscheduler-logger-server.log
```
#### [7.登录系统](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI0NjU2NDkzMQ%3D%3D%26mid%3D2247494550%26idx%3D1%26sn%3Dabdf05ddc46325b37400e66b0a5fa91a%26chksm%3De9bffabadec873ac8725efe1fda1e5f13fc304bd29c5985df4976266f86e747c47d09447ad88%26scene%3D21%23wechat_redirect&source=article&objectId=1909521)
访问前端页面地址，接口 ip (自行修改) http://192.168.xx.xx:12345/dolphinscheduler
![image.jpg](https://cdn.nlark.com/yuque/0/2024/png/29792214/1715239284972-fdf83fd6-3f72-426e-8fa9-69f6ec2adc72.png#clientId=u846a0b5a-ddc0-4&from=paste&id=u501cbb43&originalType=url&ratio=1&rotation=0&showTitle=false&size=44901&status=done&style=none&taskId=ua90efdeb-d37b-4499-8f86-2cb7ab29749&title=)
部署完成，成功进入界面。
推荐参考文档：
官网网址：
https://dolphinscheduler.apache.org/zh-cn/
官网使用教程：https://www.bilibili.com/video/BV1d64y1s7eZ
你可以在这里参考原文链接：https://blog.csdn.net/qq_50740678/article/details/120615253
本文参与 [腾讯云自媒体分享计划](https://cloud.tencent.com/developer/support-plan)，分享自微信公众号。
原始发表：2021-11-25，如有侵权请联系 [cloudcommunity@tencent.com](mailto:cloudcommunity@tencent.com) 删除
[bash](https://cloud.tencent.com/developer/tag/10178)
[bash 指令](https://cloud.tencent.com/developer/tag/10316)
[java](https://cloud.tencent.com/developer/tag/10164)
[linux](https://cloud.tencent.com/developer/tag/10308)
[云数据库 SQL Server](https://cloud.tencent.com/developer/tag/10245)
本文分享自 大数据真好玩 微信公众号，前往查看
如有侵权，请联系 [cloudcommunity@tencent.com](mailto:cloudcommunity@tencent.com) 删除。
本文参与 [腾讯云自媒体分享计划](https://cloud.tencent.com/developer/support-plan)  ，欢迎热爱写作的你一起参与！
[bash](https://cloud.tencent.com/developer/tag/10178)
[bash 指令](https://cloud.tencent.com/developer/tag/10316)
[java](https://cloud.tencent.com/developer/tag/10164)
[linux](https://cloud.tencent.com/developer/tag/10308)
[云数据库 SQL Server](https://cloud.tencent.com/developer/tag/10245)

> 来自: [保姆级超详细教程：DolphinScheduler单机(本地)部署及软件运行测试-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1909521)

