# Linux安装后的相关配置

## 一、设置网络

### 1.1设置静态地址

#### 1.1.1虚拟机的设置

（1）点击VMware虚拟机左上角的“编辑”，选择“虚拟网络编译器”。 
（2）选中VMnet8（NAT模式），再点击右侧的“NAT设置”得到ip地址段

#### 1.1.2 修改开启启动及静态IP

```
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```
将ONBOOT=no改为yes，将BOOTPROTO=dhcp改为BOOTPROTO=static,并在后面增加几行内容： 
```
IPADDR=192.168.4.188 
NETMASK=255.255.255.0 
GATEWAY=192.168.4.2 
DNS1=192.168.4.188 
```

#### 1.1.3重启网络

```
systemctl restart network.service
```

