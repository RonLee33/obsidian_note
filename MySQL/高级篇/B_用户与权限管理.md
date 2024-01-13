# 一、创建用户

## 1.1 用户的查看
创建用户之前需要，确认待创建的用户是否已存在。MySQL中不允许创建两个同样的用户。

```sql
USE mysql;
SELECT host, user FROM user;
```

MySQL数据库的安全性需要通过账户管理来保证。

> 小计 ：命令行登录MySQL小计
>  `mysql -u root -h localhost -P 3306 dbtest80 -e "select * from tbdemo80" -p`

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107161331.png)

## 1.2 创建用户

语法格式为：
```sql
CREATE USER 用户名 IDENTIFIED BY '用户密码';
```

如：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107163202.png)

上图中 `mysql.user`表中的`host`和`user`字段是联合主键，只要两者的组合不存在重复即可，可存在两个同`user`名但`host`不同的用户， 如下图：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107163558.png)

## 1.3 修改用户

```sql
UPDATE user SET user = '新用户名' [或其他存在于`mysql.user`表中的字段] WHERE user = '旧用户名' AND host = '旧主机名';
flush privileges; -- 刷新权限，在修改用户信息后，此句一定要执行，否则旧用户仍有原来的属性和权限信息
``` 

## 1.4 删除用户

```sql
DROP USER 'litan'@'localhost'; 
-- 当不带主机名时，主机名默认为 %，即，以下的两种写法是等效的
DROP USER 'litan'; 
DROP USER 'litan'@'%'; 
```

如：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107164813.png)

> 注意，不推荐使用 `DELETE FROM mysql.user WHERE user = '用户名' AND host = '主机名'`的方式删除用户，因为可能会留存已删除用户的残留信息。

# 二、修改用户密码

## 2.1 修改当前用户密码

1. 方式一：

```sql
-- USER 和 TABLE 一样，是MySQL中的一种“对象”
-- user() 函数用于获取 当前用户
ALTER USER user() IDENTIFIED BY '新密码';
```

2. 方式二：

```sql
SET password = '新密码';
```

3. 方式三（MySQL8.0中已弃用，不推荐）

```sql
SET password = password('新密码');
```

>     注意：禁止使用 `UPDATE mysql.user SET authentication_string = '新密码'`的方式直接修改`user`表中的密码字段，因为 用户密码在MySQL中是加密存储的。
>     ![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107170932.png)

## 2.2 修改其他用户的密码

能修改其他用户密码的前提是，当前用户有对应的权限。

1. 方式一：
```sql
ALTER USER 'litan'@'%' IDENTIFIED BY '新密码';
```

2. 方式二：
```sql
SET password FOR 'litan'@'%' = '新密码'
```

3. 方式三（不推荐）：
通过修改`mysql.user`表的`authentication_string`字段来使用函数`password('密码')`进行修改。
```sql
UPDATE mysql.user SET authentication_string = password('新密码') WHERE user = '用户名' AND host = '主机名'；
```

# 三、权限管理

## 3.1 查看所有权限内容

```sql
show privileges;
```

## 3.2 查看当前用户所拥有的权限

```sql
show grants;
```

> 查看某用户的全局权限 ：`show grants for 'litan'@'%'`。

## 3.3 授权原则

1. 只授予能 **满足需要的最小权限**，防止用户有意或无意错误地删除数据，如用户只需查询，则无需给其增加、修改、删除数据地权限，只给与查询地权限即可；
2. 创建用户时**限制用户地登录主机**，用于数据保密；
3. 为每个用户**设置满足一定复杂度的密码**；
4. 定期清理不需要的用户、回收权限或删除用户。

## 3.4 授权方式

### 3.4.1 直接授权

```sql
GRANT 权限 ON 数据库（数据名为 ‘*’时表示所有库）.表名（表名为 ‘*’时表示所有表）TO 用户;
```

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107180619.png)

> 直接授权时，当多次对同一用户授予不同权限时，会把每次授权权限追加，即，后一次的授权不会覆盖前一次的授权。
> 另外，可授予某用户所有权限，即`grant all privileges on *.* to 'litan'@'%'`，此时用户`litan`与`root`的区别在于，`root`能为别的用户授权，而`litan`却不能。

### 3.4.2 角色授权(MySQL8.0中支持，5.7不支持)

角色，是权限的集合，是权限的“模板”，当多个用户需要赋予同样的权限时，这些可复用的权限可封装到可复用的权限集合（即 角色）中，这样，就只需要给予对应用户相应的角色即可。

#### 3.4.2.1 创建角色

创建角色的语法格式如下：
```sql
CREATE ROLE '角色名' [@'host_name'] [, '角色名' [@'host_name'] ]...
```

如：
```sql
create role 'manager'@'%', 'boss'@'%';
```

#### 3.4.2.2 角色授权

```sql
-- grant 权限 on 数据库名.表名 to 角色名[%'主机名']
grant select, update on dbtest80.* to 'manager'；
grant all privileges on *.* to 'boss';
```

#### 3.4.2.3 查看角色权限

```sql
show grants for 'manager';
```

#### 3.4.2.4 回收角色权限

```sql
revoke SELECT, UPDATE ON `dbtest80`.* from 'manager';
```

#### 3.4.2.5 删除角色

```sql
drop role 'manager';
```

#### 3.4.2.6 将角色赋予用户

```sql
grant 'manager'@'%' to 'wangwu';
```

#### 3.4.2.7 激活角色

将角色授予用户进行绑定后，还需要手动激活相应角色，使角色生效，激活角色后，对应用户需要退出`MySQL`，重新登录，之后便拥有对应角色相应的权限了。激活角色时，用户可以为自己激活角色。

1. 方式一：
```sql
set default role 'manager'@'%' to 'wangwu'@'%';

-- 另外，也可将已创建的所有角色同时赋予多个用户
set default role all to 
'用户名1'@'主机名1',
'用户名2'@'主机名2',
'用户名3'@'主机名3',
...
'用户名n'@'主机名n';
```

2. 方式二：将`activate_all_roles_on_login`设置为`ON`
```sql
set global activate_all_roles_on_login=ON;
```
`activate_all_roles_on_login`默认为 `OFF`，如下图：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240108200434.png)

#### 3.4.2.8 撤销用户角色

对用户撤销角色时，用户不能自己撤销自己的角色。撤销角色后，用户无需重新登录，即刻生效，但用户退出前仍能行使已撤销角色的权限，用户重登后，无法行使权限。语法格式如下：
```sql
-- revoke '角色名' from '用户名' --> 等价于 revoke '角色名'@'%' from '用户名'@'%';
revoke 'manager' from 'wangwu';
```

#### 3.4.2.9 设置强制角色(`mandatory role`)

强制角色是给每个新用户刚被创建时的默认角色，无需手动设置。强制角色无法被`REVOKE`或`DROP`。
1. 启动`MySQL`服务前在`my.cnf`中设置
```sql
mandatory_roles = '角色名1@主机名1, 角色名2@主机名2, ... , 角色名n@主机名n'
```
2. 运行时设置
```sql
-- MySQL重启后仍有效，MySQL8.0新特性
set persist mandatory_roles = '角色名1@主机名1, 角色名2@主机名2, ... , 角色名n@主机名n';
-- MySQL重启后失效
set global mandatory_roles = '角色名1@主机名1, 角色名2@主机名2, ... , 角色名n@主机名n';
```

#### 3.4.2.10 角色使用流程回顾

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240108200730.png)
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240108200747.png)

## 3.5 权限回收

```sql
REVOKE 权限 ON 数据库（数据名为 ‘*’时表示所有库）.表名（表名为 ‘*’时表示所有表）FROM 用户;
```

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240107182404.png)

