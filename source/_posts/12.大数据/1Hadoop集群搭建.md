## 集群规划

![image](https://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/blog/DDdMQe.png)

## 端口说明

| 端口名称                 | Hadoop2.X   | Hadoop.x         |
| ------------------------ | ----------- | ---------------- |
| NameNode 内部通信端口    | 8020 / 9000 | 8020 / 9000/9820 |
| NameNode HTTP UI         | 50070       | 9870             |
| MapReduce 查看执行任务端 | 8088        | 8088             |
| 历史服务器通信端口       | 19888       | 19888            |



## 创建容器

| a    | hadoop02           | hadoop03                             | hadoop04                    | Hadoop04 |
| ---- | ------------------ | ------------------------------------ | --------------------------- | -------- |
| HDFS | NameNode，DataNode | DataNode                             | SecondaryNameNode，DataNode |          |
| Yarn | NodeManager        | ResourceManager，NodeManager         | NodeManager                 |          |
| Port |                    | 19888，9870                          | 8088                        | 9868     |
|      |                    | hdfs namenode -format 和start-dfs.sh | start-yarn.sh               |          |



## 开始执行命令

#### 创建网络

```she
docker network create -d bridge net --subnet=172.18.0.0/24 --gateway=172.18.0.1
```



#### hadoop01

```shell
docker run -itd --privileged=true --name  hadoop01 --net net --ip  172.18.0.11 guozhenhua/hadoop:5 /usr/sbin/init
```

#### Hadoop02

```shell
docker run -itd --privileged=true --name  hadoop02 --net net --ip  172.18.0.12 -p 9870:9870 -p 19888:19888 guozhenhua/hadoop:5   /usr/sbin/init
```

#### Hadoop03

```shell
docker run -itd --privileged=true --name  hadoop03 --net net --ip  172.18.0.13 -p 8088:8088 guozhenhua/hadoop:3   /usr/sbin/init
```

#### Hadoop04

```shel
docker run -itd --privileged=true --name  hadoop04 --net net --ip  172.18.0.14 -p 9868:9868 guozhenhua/hadoop:3   /usr/sbin/init
```

### 在hadoop02上

- 切换用户：su hadoop
- 执行： hdfs namenode -format
- 执行：start-dfs.sh

#### 在hadoop03上

- 切换用户：su hadoop
- 执行：start-yarn.sh

![image](https://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/blog/MMytru.png)

#### 测试

- hadoop fs -mkdir /input

![image](https://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/blog/khpzp7.png)

- 访问页面：http://127.0.0.1:9870/explorer.html#/input

![image](https://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/blog/SdEF7y.png)



## 搭建过程回顾

### 1. 构建环境

- 下载centos:7容器。

  ```shell
  docker pull centos:7
  ```

- 创建用户

  ```shell
  useradd hadoop
  passwd hadoop
  ```

- 配置JAVA的环境。

  ```shell
  下载jdk，配置JAVA_HOME
  ```

- 配置hadoop的环境。

  ```shell
  下载hadoop软件即可： https://ftp.jaist.ac.jp/pub/apache/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz
  ```

  

- 设置ssh免密码登陆

  ```shell
  默认是不存在ssh登陆的，所以需要安装openssh服务yum install install openssh-client openssh-server修改配置 vim /etc/ssh/sshd_config
  ```

  

- 根据集群规划配置/etc/hosts

  ```shel
  按照IP配置172.18.0.11  hadoop01172.18.0.12  hadoop02172.18.0.13  hadoop03172.18.0.14  hadoop04
  ```

  

- 配置hadoop的配置

  > 配置 core-site.xml

  ```xml
  <configuration>
    <!-- 指定 NameNode 的地址 -->  
    <property>     
      <name>fs.defaultFS</name>    
      <value>hdfs://hadoop02:8020</value> 
    </property> 
    <!-- 指定 hadoop 数据的存储目录 --> 
    <property> 
      <name>hadoop.tmp.dir</name>  
      <value>/home/hadoop/data</value>  <
      /property> 
      <!-- 配置 HDFS 网页登录使用的静态用户为 --> 
      <property> 
        <name>hadoop.http.staticuser.user</name>
        <value>hadoop</value>  
      </property>
   </configuration>
  ```

  

  > 配置hdfs-site.xml

  ```xml
  <configuration>
    <property>	   
      <name>dfs.namenode.http-address</name>       
      <value>hadoop02:9870</value>      
    </property>
    <!-- 2nn web 端访问地址-->      
    <property>       
      <name>dfs.namenode.secondary.http-address</name>       
      <value>hadoop04:9868</value>    
    </property>
  </configuration>
  ```

  > 配置 yarn-site.xml

  ```xml
  <configuration> 
    <!-- Site specific YARN configuration properties -->  
    <!-- 指定 MR 走 shuffle -->  
    <property>
      <name>yarn.nodemanager.aux-services</name>  
      <value>mapreduce_shuffle</value> 
    </property>
    <!-- 指定 ResourceManager 的地址-->  
    <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>hadoop03</value> 
    </property>
    <!-- 环境变量的继承 -->  
    <property> 
      <name>yarn.nodemanager.env-whitelist</name>  
      <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CO NF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAP RED_HOME</value> 
    </property>
  </configuration>
  
  ```

  > 配置 mapred-site.xml

  ```xml
  <configuration>
    <property>
      <name>mapreduce.framework.name</name>  
      <value>yarn</value> 
    </property>
  </configuration>
  ```

  > 配置 workers

  ```xml
  hadoop02
  hadoop03
  hadoop04
  ```

## docker-compose 配置

> 以下是直接搭建好的集群环境，可以直接使用 docker-compose 启动容器。
>
> 启动容器后需启动hadoop集群
>
> ### 在hadoop02上
>
> - 切换用户：su hadoop
> - 执行： hdfs namenode -format
> - 执行：start-dfs.sh
>
> #### 在hadoop03上
>
> - 切换用户：su hadoop
>
> - 执行：start-yarn.sh
>
>   

```yml
version: "3"

services:
  hadoop02:
    image: guozhenhua/hadoop:5
    container_name: hadoop02
    privileged: true
    ports:
      - 9870:9870
    networks:
      net1:
        ipv4_address: 172.18.0.12
    extra_hosts:
      - "hadoop03:172.18.0.13"
      - "hadoop02:172.18.0.12"
      - "hadoop04:172.18.0.14"
    # user: hadoop
    volumes:
      - hadoop02:/home/hadoop/data
    # command: hdfs namenode -format &&  start-dfs.sh

  hadoop03:
    image: guozhenhua/hadoop:5
    container_name: hadoop03
    privileged: true
    ports:
      - 8088:8088
    networks:
      net1:
        ipv4_address: 172.18.0.13
    extra_hosts:
      - "hadoop03:172.18.0.13"
      - "hadoop02:172.18.0.12"
      - "hadoop04:172.18.0.14" 
    volumes:
      - hadoop03:/home/hadoop/data
    # user: hadoop
    # command: start-yarn.sh  

  hadoop04:
    image: guozhenhua/hadoop:5
    container_name: hadoop04
    privileged: true
    ports:
      - 9868:9868
    networks:
      net1:
        ipv4_address: 172.18.0.14
    extra_hosts:
      - "hadoop03:172.18.0.13"
      - "hadoop02:172.18.0.12"
      - "hadoop04:172.18.0.14"
    volumes:
      - hadoop04:/home/hadoop/data
    # user: hadoop

networks:
  net1:
    driver: bridge
    ipam:
      config:
        - subnet: 172.18.0.0/24
          gateway: 172.18.0.1
  
volumes:
  hadoop02:
    driver: local
  hadoop03:
    driver: local
  hadoop04:
    driver: local
```

**注意：** 

- 配置文件中的**IP地址和container_name **不可随意更改。如需要更改，需要修改容器中的/etc/hosts。
- privileged: true 这个是必须的，否则会导致sshd服务不可启动，集群启动无法连接登陆。





