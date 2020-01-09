# nexus-create
# 搭建Maven仓库

## 开启root用户运行nexus

vim etc/profile 在该文件最下方 加入:

export RUN_AS_USER=root

## 运行

进入到bin目录，执行： ./nexus start

## 设置开机启动

创建连接到 /etc/init.d下

```
sudo ln -s /usr/local/nexus-2.11.2-03/bin/nexus /etc/init.d/nexus
```

查看nexus服务状态、启动服务、停止服务等

```
service nexus status/start/stop
```

设置nexus服务开机自启动或者开机不启动

```
chkconfig nexus on/off
```

###  systemctl start nexus出现异常，可使用如下解决方案：

这个问题很简单，只需要修改一下 nexus 的配置文件就可以了，根据错误提示内容，在配置文件中找到 “ INSTALL4J_JAVA_HOME_OVERRIDE ” 项，取消其注解，并将地址指向本地的 Jre 环境即可

## 调整配置

下载解压文件后：配置bin目录下nexus.vmoptions文件，适当调整内存参数，防止占用内存太大

![image-20200107214215399](assets/image-20200107214215399.png)

## 添加代理

1. aliyun
http://maven.aliyun.com/nexus/content/groups/public
2. apache_snapshot
https://repository.apache.org/content/repositories/snapshots/
3. apache_release
https://repository.apache.org/content/repositories/releases/
4. atlassian
https://maven.atlassian.com/content/repositories/atlassian-public/
5. central.maven.org
http://central.maven.org/maven2/
6. datanucleus
http://www.datanucleus.org/downloads/maven2
7. maven-central （安装后自带，仅需设置Cache有效期即可）
https://repo1.maven.org/maven2/
8. nexus.axiomalaska.com
http://nexus.axiomalaska.com/nexus/content/repositories/public
9. oss.sonatype.org
https://oss.sonatype.org/content/repositories/snapshots
10.pentaho
https://public.nexus.pentaho.org/content/groups/omni/

####  设置maven-public 将这些代理加入Group，最好将默认的maven库放到最底下



# 添加npm仓库

## 添加npm代理

## 1，创建blob存储。

为其创建一个单独的存储空间。

![image-20200109213250359](assets/image-20200109213250359.png)

## 2，创建hosted类型的npm。

- `Name`: 定义一个名称local-npm
- `Storage`：Blob store，我们下拉选择前面创建好的专用blob：npm-hub。
- `Hosted`：开发环境，我们运行重复发布，因此Delpoyment policy 我们选择Allow redeploy。这个很重要！

![image-20200109213309801](assets/image-20200109213309801.png)

## 3，创建一个proxy类型的npm仓库。

- `Name`: proxy-npm
- `Proxy`：Remote Storage: 远程仓库地址，
- 这里填写:1. [https://registry.npmjs.org](http://www.eryajf.net/go?url=https://registry.npmjs.org) 2. https://registry.npm.taobao.org
- `Storage`: npm-hub。

其他的均是默认。

整体配置截图如下：

![image-20200109213325411](assets/image-20200109213325411.png)

## 4，创建一个group类型的npm仓库。

- `Name`：group-npm
- `Storage`：选择专用的blob存储npm-hub。
- `group` : 将左边可选的2个仓库，添加到右边的members下。

整体配置截图如下：

![image-20200109213351142](assets/image-20200109213351142.png)

这些配置完成之后，就可以使用了。

## 5，验证使用。

新建一台环境干净的主机，安装好node环境。

### 1，首先获取默认的仓库地址：

1. npm config get registry

### 2，配置为私服地址。

从如下截图中查看(其实就是创建的组对外的地址)。

![image-20200109213651863](assets/image-20200109213651863.png)

通过如下命令配置：

1. npm config set registry http://192.168.31.134:8081/repository/group-npm/
2. npm config get registry
3. http://192.168.31.134:8081/repository/group-npm/

现在开始安装，安装之前先看一下组里的内容：

![image-20200109213734384](assets/image-20200109213734384.png)

可以看到还是空的。

### 3，安装编译。

1. npm install

在编译的过程中，我们已经可以看看组里的变化了：

![image-20200109213748555](assets/image-20200109213748555.png)

安装完成，整个过程如下，可以看到一共花费了`82秒`。

1. npm install

### 4，再一次安装编译。

这里再准备一台环境干净的主机，然后进行一次编译安装，看看效果。

编译之前，先将远程地址配置为我们自己的：

1. npm config get registry
3. npm config set registry http://192.168.112.214:8081/repository/group-npm/
4. npm config get registry
5. http://192.168.112.214:8081/repository/group-npm/