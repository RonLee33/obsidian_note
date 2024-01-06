# 一、Windows下的数据库与目录结构的对应关系

## 1.1 数据库与目录的对应关系

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106145427.png)

## 1.2 表与目录的对应关系

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106145558.png)

## 1.3 MySQL 服务程序所在目录

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106150048.png)


# 二、Linux下的数据库与目录结构的对应关系

## 2.1 数据库与目录的对应关系

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106151714.png)

## 2.2 MySQL 命令所在目录

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106151913.png)

## 2.3 配置文件目录

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106152419.png)

## 2.4 MySQL 5.7中InnoDB的存储结构

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106155905.png)

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106155922.png)

## 2.5 MySQL 8.0中InnoDB的存储结构

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106161553.png)

查看 tbdemo80.ibd是否存储表结构

1. 解析`.ibd`文件：`ibd2sdi --dump-file=tbdemo80.txt tbdemo80.ibd` ；
2. 查看结果文件`tbdemo80.txt`，如下：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106162532.png)

## 2.6 MyISAM在5.7和8.0中的存储结构

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20240106165249.png)
