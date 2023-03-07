## wsl2

### 安装

win11只需要执行`wsl --install`，win10需要2004及以上版本来执行一下步骤。

1. 启用wsl

	``` powershell
	dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
	#对应控制面板-程序和功能-启用或关闭windows功能-适用于Linux的Windows子系统
	```

2. 启用虚拟机功能

	WSL 2 需要启用 Windows 10 的虚拟机平台特性，它独立于 Hyper-V。

	``` powershell
	dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
	##对应控制面板-程序和功能-启用或关闭windows功能-虚拟机平台
	```

	然后重启计算机

3. 下载 Linux 内核更新包

	``` powershell
	wsl --update
	
	#查看wsl版本
	wsl --status
	#如果不是wsl2，可以设置wsl2为默认值
	wsl --set-default-version 2
	```

	[或者手动下载安装](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

4. 安装Linux发行版

	[从Microsoft Store中选择并安装Ubuntu](https://www.microsoft.com/store/apps/9n6svws3rx71)

5. 一些命令

	``` bash
	#列出已安装的 Linux 发行版
	wsl --list --verbose
	```

  ### 设置

**1）修改默认root登录**

1. 为root设置密码

	``` bash
	#wsl创建完成后，是通过自定义的用户和密码进行登录的，需要为root设置密码
	sudo passwd  root
	```


2. 修改为默认root登录

  进入到`ubuntu2204.exe`的路径内 
  C:\Program Files\WindowsApps\CanonicalGroupLimited.Ubuntu22.04LTS_2204.2.37.0_x64__79rhkp1fndgsc\ubuntu2204.exe 执行

  ``` bash
  ubuntu2204.exe config --default-user root
  ```

  >WindowsApp因为是Windows商店UWP的目录，所以因为权限进不去，可以用powershell cd进去

**2）为root用户增加配色**

``` bash
#查看所有用户
cat /etc/shadow
#删除用户
userdel ming
#如果想把用户的文件夹也删除，需要带上r参数
userdel -r ming
#增加用户
adduser ming
#root用户界面没有配色，需要把非root用户的配色文件复制到root的目录下
cp /home/ming/.bashrc /root/.bashrc
```

**3）修改主机名**

使用 `hostnamectl set-hostname --static "ming"` 修改主机名时，重启wsl会恢复默认
需要在 `/etc/wsl.conf` 中修改

``` bash
#没有就创建一个wsl.conf
vim /etc/wsl.conf

#输入一下参数，注意[network]别漏打，hostname：设置当前 wsl 的主机名称；generateHosts：是否自动生成 hosts 文件
[network]
hostname = node01
generateHosts = false
#然后重启wsl --shutdown

#也可以在此修改默认登陆用户，优先级高于ubuntu2204.exe config --default-user xxx 命令
[user]
default = root
```



### systemd

微软宣布已与 Canonical 合作，将 systemd 引入 WSL

[systemd入门教程](https://ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

启用systemd

``` bash
echo -e "[boot]\nsystemd=true" | sudo tee -a /etc/wsl.conf
```

判断systemd是否启用成功

``` bash
ps --no-headers -o comm 1
# 如果命令返回的是init说明systemd未启用，如果是systemd那么你的systemd已启用成功了。
```



## 安装doker

[官网文档](https://docs.docker.com/engine/install/ubuntu/)

**1）卸载旧版本**

``` bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

**2）使用apt安装**

以下命令会添加稳定版本的 Docker APT 镜像源

1. 由于 `apt` 源使用 HTTPS 以确保软件下载过程中不被篡改。因此，我们首先需要添加使用 HTTPS 传输的软件包以及 CA 证书。

	``` bash
	sudo apt-get update
	
	sudo apt-get install \
		ca-certificates \
	    curl \
	    gnupg \
	    lsb-release
	```

2. 添加docker官方GPG

	``` bash
	sudo mkdir -m 0755 -p /etc/apt/keyrings
	
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
	
	#也可以使用国内源，提高软件下载速度
	curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
	```

3. 向 `sources.list` 中添加 Docker 软件源

	``` bash
	echo \
	  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
	  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
	  
	#国内源为
	echo \
	  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
	  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
	```

**3）安装Docker Engine**

``` bash
#更新apt软件包缓存
sudo apt-get update
#安装最新版的Docker Engine, containerd,和Docker Compose.
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
#运行hello-world images查看docer engine是否安装成功
service docker start
sudo docker run hello-world
```

**4）换镜像源**

拉取镜像慢，可以使用国内的 Docker Hub 镜像服务器。

``` bash
#编辑/etc/docker/daemon.json配置文件，如果目录和文件不存在，会自动创建
vim /etc/docker/daemon.json
```

``` json
{
  "registry-mirrors": [
    "https://cr.console.aliyun.com/",
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```



## vim

**查找**

1. 需要处于normal模式，/向前搜索，？向后搜索，按enter进行搜索
2. n表示搜索下一个结果，N表示搜索前一个结果

**删除**

1. gg：光标跳转到该文件的行首

2. dG：删除光标行及其以下行的全部内容。（注：d为删除，G为光标跳转到末尾行）
