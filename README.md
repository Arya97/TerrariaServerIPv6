# 泰拉瑞亚原版IPv6服务器/客户端配置教程（以Ubuntu虚拟机为例）

## 现在最前
泰拉瑞亚原版（版本1.4.5.6）服务器和客户端仅支持IPv4，而如今普通家庭几乎没有办法获取公网IPv4地址，相比之下公网IPv6地址比较好申请，同时IPv6联机相比Steam直连或内网穿透延迟更低。

本项目为基于IPv6开设泰拉瑞亚原版服务器的教程，目标对象为拥有公网IPv6地址开设服务器的用户，包含客户端的配置和连接方式。

### 注意
1. 每个人的网络环境不同，略折腾，谨慎选择该方式，云服务器或者tMod（原生支持IPv6）或许是更好的选择
2. 利用IPv6联机无论是服务器端和客户端均需要支持IPv6，请提前做好测试以免白忙活

## 前置条件
请确认自己拥有**公网IPv6地址**

## 虚拟机网卡模式（独立的服务器可忽略）
使用任意虚拟机初始化Ubuntu网卡时选择桥接模式确保和宿主机在同一网段，方便宿主机作为客户端连接

## 服务端配置

### 1. 网关防火墙
路由器中IPv6防火墙关闭或防护级别调低

防火墙是否需要调整可通过外网ping测试，如果可以ping通不需要调整

**Windows**
```powershell
ping -6 公网IPv6地址 
```

**MacOs**
```bash
ping6 -c 5 公网IPv6地址
```

以中兴BE5100Pro+为例：IPv6防火墙设置为低可以ping通

### 2. Ubuntu防火墙放行端口

查看当前防火墙状态（VMWare上虚拟的Ubuntu防火墙状态默认关闭，如不想开启，检查状态发现是关闭可跳过后续步骤)
```bash
sudo ufw status 
```
如果显示以下信息说明防火墙未开启
```bash
Status: inactive
```

设置防火墙（泰拉瑞亚使用tcp连接，理论上第二行udp的可以不输入）

7777 为泰拉瑞亚默认端口，可以自定义
```bash
sudo ufw allow 7777/tcp
sudo ufw allow 7777/udp
sudo ufw enable
sudo ufw status 
```
再次查看防火墙信息应如下
```bash
Status: active

To                         Action      From
--                         ------      ----                 
7777/udp                   ALLOW       Anywhere                  
7777/tcp                   ALLOW       Anywhere                                  
7777/udp (v6)              ALLOW       Anywhere (v6)             
7777/tcp (v6)              ALLOW       Anywhere (v6) 
```
### 3. 固定服务器的内网IP
以中兴BE5100Pro+为例：

#### 网络 - 局域网配置 - 静态地址绑定 - 新建条目

名称：任意你喜欢的名称，最好能标记作为服务器的Ubuntu

MAC地址：从已关联设备中选择，选择作为服务器的Ubuntu

IP 地址：通过网段下的内网IPv4地址 如192.168.5.210

网关：一般已填充且为路由器控制界面地址，不需要改。如果未填充输入路由器控制界面如192.168.5.1

### 4. 查看本机IPv6地址

#### 查看本机ip信息

```bash
ip a
```

找到2开头的后续有scope global dynamic字样的公网IPv6地址

#### 偷懒版本
```bash
ip a | grep -inet6 | grep -v fe80 | grep -v ::1
```

应只有一条信息且后缀有scope global dynamic如下

```bash
11:    inet6 240e:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx/128 scope global dynamic noprefixroute
```



### 5. 通过tinymapper做IPv6-IPv4转换

```bash
./tinymapper_amd64 -l[公网IPv6地址]:7777 -r127.0.0.1:7777 -t
```

### 6. 开启服务器
#### 直接开服
```bash
./tinymapper_x86_64
```
#### 读取保存配置开服（TODO）

### 7. 存档迁移
```bash
scp "C:\Users\Admin\Documents\My Games\Terraria\Worlds\World Name.wld" "C:\Users\Admin\Documents\My Games\Terraria\Worlds\World Name.wld.bak" "C:\Users\Admin\Documents\My Games\Terraria\Worlds\World Name.wld.bak2" username@192.168.5.210:~/.local/share/Terraria/Worlds
```

## 客户端配置

### 通过tinymapper做IPv4-IPv6转换

### Windows(宿主机)
直接通过服务器内网地址+端口进入，上文设置为192.168.5.210:7777

#### 打开泰拉瑞亚

输入IP地址
```bash
192.168.5.210
```
端口
```bash
7777
```


### Windows
暂时没有用Windows外网设备测试

### MacOs

#### 开启Terminal并输入(本地127.0.0.1端口可自定义，后续通过该端口联机)

```bash
./tinymapper_mac -l 127.0.0.1:7777 -r '[公网IPv6地址]:7777' -t
```

#### 打开泰拉瑞亚

输入IP地址
```bash
127.0.0.1
```
端口
```bash
7777
```

### 手机（以IOS为例）
#### 国际服IOS客户端原生支持IPv6，直接输入服务器的公网IPv6地址和端口即可联机

输入IP地址
```bash
公网IPv6地址
```
端口
```bash
7777
```