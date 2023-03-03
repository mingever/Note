## wsl2

### win11

只需执行`wsl --install`

### win10

需要win10 2004及以上版本

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

  或者手动下载安装： https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

4. 安装Linux发行版

  从Microsoft Store中选择并安装Ubuntu： https://www.microsoft.com/store/apps/9n6svws3rx71

5. 一些命令

  ``` powershell
#列出已安装的 Linux 发行版
wsl --list --verbose
  ```

  

  ### 用户设置

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

3. 为root用户增加配色

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

	

## vim

### 查找

1. 需要处于normal模式，/向前搜索，？向后搜索，按enter进行搜索
2. n表示搜索下一个结果，N表示搜索前一个结果

### 删除

1. gg：光标跳转到该文件的行首

2. dG：删除光标行及其以下行的全部内容。（注：d为删除，G为光标跳转到末尾行）
