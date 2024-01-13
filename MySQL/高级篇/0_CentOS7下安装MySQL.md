# 一、前期准备

## 1.1 缓存目录授权

```sh
chmod -R o=rwx /tmp
```

## 1.2 依赖检查

1. `libaio`
```sh
rpm -qa | grep libaio
```
若存在依赖，会显示：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113155229.png)

2. `net-tools`
```sh
rpm -qa | grep net-tools
```

3. 当前两步中有任意依赖不存在时，安装对应依赖即可，命令如下：
```sh
# yum install [依赖名]
yum install net-tools
```
如：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113160911.png)

# 二、安装MySQL

## 2.1 将安装包文件上传到/opt目录下

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113162154.png)

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113162125.png)

## 2.2 按以下顺序依次安装（顺序一定要对）

```sh
rpm -ivh mysql-community-common-8.0.25-1.el7.x86_64.rpm
# MySQL5.7 中不存在 mysql-community-client-plugins-xxx.rpm的包，其余存在的安装包的安装顺序同MySQL8.0
rpm -ivh mysql-community-client-plugins-8.0.25-1.el7.x86_64.rpm
# 第3句报错时，可用 yum remove mysql-libs 解决
rpm -ivh mysql-community-libs-8.0.25-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.25-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.25-1.el7.x86_64.rpm --nodeps --force
```

验证安装成功：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113165938.png)

三、MySQL服务初始化

为保证数据库目录与文件的所有者为mysql登录用户，若是以root身份运行MySQL服务，则需运行以下命令进行初始化：
```sh
mysqld --initialize --user=mysql
```
之后查看`mysqld.log`中初始化的`MySQL`的`root`登录密码：
```sh
cat /var/log/mysqld.log
```
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113170707.png)

# 四、MySQL的启动及配置

## 4.1 启动MySQL服务
```sh
systemctl start mysqld
```

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113171105.png)

## 4.2 设置MySQL服务开机自启动

1. 查看是否已设置为自启动，`enabled`表示已为自启动：
```sh
systemctl list-unit-files | grep mysqld.service
```
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113171431.png)

2. 设置为自启动
```sh
systemctl enable mysqld.service
```

3. 设置为非自启动
```sh
systemctl disable mysqld.service
```

## 4.3 登录MySQL并重置root用户密码

初始化提供的默认密码只供初次登录供重置密码用，无法用初始密码登录进行查询。
修改密码：
```mysql
alter user 'root'@'localhost' identified by 'lt199729@';
```

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113172216.png)

## 4.4 配置MySQL服务器字符集(仅MySQL5.7需要配置)

```sh
vi /etc/my.cnf
```

在配置文件的`[mysqld]`选项组中添加`character_set_server=utf8`，保存配置后，重启mysql服务（`systemctl restart mysqld.service`）即可。
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113193526.png)


# 五、防火墙端口开放3306

1. 查看防火墙状态
```sh
firewall-cmd --state
```

2. 永久指定开放端口
```sh
# --add-port 改为 --remove-port 时，是关闭端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent
```

3. 重新加载防火墙配置
```sh
firewall-cmd --reload
```

4. 确认端口已开放
```sh
firewall-cmd --zone=public --list-ports
```

如下图：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240113211239.png)
