# node.js



1. 下载安装

	https://nodejs.org/en/

	http://nodejs.cn/download/

2. 全局配置

	配置npm安装的全局模块所在的路径，以及缓存cache的路径，不然执行`npm install XXX -g` global全局安装时，会安装到C:\Users\用户名\AppData\Roaming\npm中

	``` bash
	#在node.js安装文件夹下，创建node_global和node_cache文件夹，然后执行下面的
	npm config set prefix "D:\install\nodejs\node_global"
	npm config set cache "D:\install\nodejs\node_cache"
	```

	> ==配置完成后会在用户目录下生成.npmrc文件，不要删除==

3. 配置环境变量

	将`D:\install\nodejs\node_global`和`D:\install\nodejs\node_global\node_modules` 添加到path中

4. npm

	node.js自带了软件安装包管理工具npm

	``` bash
	#在管理员权限下
	
	#查看nodejs版本
	node -v
	#查看npm版本
	npm -v
	
	#升级 npm
	npm install -g npm
	#使用淘宝的镜像及其命令cnpm
	#升级或安装 cnpm
	npm install cnpm -g
	
	#查看全局已安装
	npm ls -g
	#查看当前项目已安装包（项目跟目录必须有 package.json 文件）
	npm ls
	#卸载全局包
	npm uninstall 
	```

  

# vue3

## 起步

构建（以管理员启动）

- 直接通过idea执行`npx create-vue` 即可构建一个基于vite的vue3项目
- 在命令行`npm init vue@latest`  即会安装并执行`create-vue`，它是Vue官方的项目脚手架工具（vue cli已停止更新）
	- pinia：类似于vuex的状态管理库
	- ESLint：代码检测工具，解决代码质量问题
	- Prettier：代码格式化工具，解决代码风格问题
	- package.json：它是项目的配置文件，常见的配置有配置项目启动、打包命令，声明依赖包等。它不会自动生成，需要我们使用命令创建，npm 为我们提供了创建 package.json 文件的命令 npm init。
	- package-lock.json：是自动生成的，我们使用 npm install 安装包后就会自动生成。package.json里面的包版本不是一个具体的版本，而是一个最优版本，而package-lock.json里面定义的是某个包的具体版本，以及包之间的层叠关系。
