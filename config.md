# windows Terminal

## git

### git配置

1. 配置账户

	``` bash
	git config --global user.name "ming"
	git config --global email.name "1697361914@qq.com"
	```

2. 生成ssh公钥

	``` bash
	ssh-keygen -t rsa -C "1697361914@qq.com"
	#然后一直按，最终会在C:\Users\ming\.ssh里生成一对公私钥，公钥为id.rsa.pub，复制里面的内容到github即可
	```

3. known_hosts

	当通过SSH连接到一个新的远程服务器时（如第一次clone时），系统会提示你是否要将远程主机添加到 known_hosts 文件
	选择 yes，服务器的连接信息会保存在你的系统中。

	known_hosts文件存储用户访问的主机的公钥。这是一个非常重要的文件，它通过将用户的身份保存到本地系统来确保用户连接到合法的服务器。这也有助于避免中间人攻击。



### 在 PowerShell 中使用 Git

1. 设置脚本权限

	``` powershell
	Set-ExecutionPolicy -Scope LocalMachine -ExecutionPolicy RemoteSigned -Force
	```

2. 使用包管理器安装posh-git

	``` powershell
	Install-Module posh-git -Scope CurrentUser -Force
	```

3. 更新 PowerShell 提示符

	要在提示符中包含 Git 信息，那么需要 `Import-Module posh-git` 导入 Posh-Git 模块。
	使用 `Add-PoshGitToProfile` 命令，来要让 PowerShell 在每次启动时都自动导入 Posh-Git，它会在你的 $profile 脚本中添加导入语句，此脚本会在每次打开新的 PowerShell 终端时执行。

	``` powershell
	Import-Module posh-git
	Add-PoshGitToProfile -AllHosts
	```

4. 因为上述命令生成的脚本会在每次打开新的 PowerShell 终端时执行，因此可以在此脚本最后加上 `clear` 来消除每次打开windows terminal都会出现的版本信息。上述产生的脚本位置为：Documents\WindowsPowerShell\profile.ps1



### git 中文文件名乱码

是因为git 默认中文文件名是 \xxx\xxx 等八进制形式

``` bash
git config --global core.quotepath false
```



## WSL2

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

  或者手动下载安装

  https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

4. 安装Linux发行版

  从Microsoft Store中选择并安装Ubuntu

  https://www.microsoft.com/store/apps/9n6svws3rx71

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
