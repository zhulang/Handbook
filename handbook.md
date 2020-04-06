##一.手机越狱
1.备份数据

2.进入DFU模式（以iPhone 6 Plus为例）

	(1)关闭手机
	(2)按住电源键出现Apple Logo时立刻再按住HOME键
	(3)进入黑屏时，松开电源键
	(4)持续10s进入DFU模式
3.下载安装checkra1n越狱，https://checkra.in/

4.安装cydia

5.安装插件
	
	(1)openSSH:用于Mac和iPhone连接访问
	(2)Reveal2Loader:用于析构APP图形界面(在通用->设置 中将Enabled Applications打开)
	(3)AppList:用于读取iPhone安装的软件列表
	(4)AppSync Unfified:安装ipa包
	(5)AFC2: 用于访问iOS设备文件系统（Apple File Conduit/afc2add
	(6)adv-cmds:用于cycript(混合语法语言)命令行调试工具
	
##二.Mac工具安装

1.iFunBox:用于在Mac端浏览iPhone文件系统

2.Reveal:用于析构APP页面

3.Hopper Disassembler:静态分析，可执行文件转汇编

4.MachOView:用于查看可执行文件的内存分布

5.Clutch:用于脱壳

##三.OpenSSH远程登录

1.wifi环境

		（1）登录ssh root@192.168.0.199 (192.168.0.199iOS设备在局域网里的ip地址)
		（2）原始密码：alpine
		（3）修改密码：password 、password mobile
		（4）删掉之前链接的公钥信息：ssh-keygen -R 服务器IP地址 或 vim ~/.ssh/known_hosts
		
2.USB环境

1.下载usbmuxd工具包（包含python脚本：tcprelay.py，下载地址：https://cgit.sukimashita.com/usbmuxd.git/snapshot/usbmuxd-1.0.8.tar.gz）

2.将iPhone的22端口（SSH端口）映射到Mac的10010端：

	cd ~/usbmuxd-1.0.8/python-client
	python tcprelay.py -t 22:10010
	
3.打开终端界面

	ssh root@localhost -p 10010
	ssh root@127.0.0.1 -p 10010 //最好IP地址用IPV4
	
4.将命令行放入shell脚本文件中，通过sh、bash或者source指令来执行，例如：sh usb.sh (source指令会cd到脚本文件坐在目录下执行脚本)

5.iOS端中文字符支持

	//新建一个~/.inputrc文件
	//不将中文字符转化为转义序列
	set convert-meta off 
	
	//允许向终端输出中文
	set output-meta on
	
	//允许向终端输入中文
	set meta-flag on 
	set input-meta on
	
##四.Cycript
1.基本用法

	//开启
	cycript
	cycript -p 进程ID
	cycript -p 进程名称
	
	取消：Ctrl + C
	退出：Ctrl + D
	清屏：Command + R
	
2.ps指令，找出当前线程

	//ps命令是process status的缩写，使用ps命令可以列出系统当前的进程
	列出所有的进程
	ps –A
	ps aux
	
	//搜索关键词
	ps –A | grep 关键词
	
##五.class-dump
1.导出头文件

	class-dump  -H  Mach-O文件路径  -o  输出文件目录
	
##六.Hopper Disassmbler
1.常用快捷键

	找出指定方法：Shift + Option + X
	
##七.Mach-O文件
1.文件包含的类型

	1.MH_OBJECT
		目标文件（.o）
		静态库文件(.a），静态库其实就是N个.o合并在一起
	
	2.MH_EXECUTE：可执行文件
		.app/xx
	
	3.MH_DYLIB：动态库文件
		.dylib
		.framework/xx
	
	4.MH_DYLINKER：动态链接编辑器
		/usr/lib/dyld
	
	5.MH_DSYM：存储着二进制文件符号信息的文件
		.dSYM/Contents/Resources/DWARF/xx（常用于分析APP的崩溃信息）
		
2.内部结构

	1.HEADER 
	文件类型、目标架构类型等
	
	2.LOAD COMMANDS
	描述文件在虚拟内存中的逻辑结构、布局
	
	3.RAW SEGMENT DATA
	在Load commands中定义的Segment的原始数据
	
3.相关工具

	1.命令行工具
	file：查看Mach-O的文件类型
	常用命令：file 文件路径
	
	2.otool：查看Mach-O特定部分和段的内容
	
	3.lipo：常用于多架构Mach-O文件的处理
	查看架构信息：lipo  -info  文件路径
	导出某种特定架构：lipo  文件路径  -thin  架构类型  -output  输出文件路径
	合并多种架构：lipo  文件路径1  文件路径2  -output  输出文件路径
	
	4.GUI工具:MachOView
	
4.dyld用于加载MH_EXECUTE、MH_DYLIB、MH_BUNDLE类型的Mach-O文件

##八.脱壳
1.工具:
	
	Clutch、dumpdecrypted、CrackerXI(我采用第三个工具)
2.是否脱壳

	1.查看Mach-O文件Load Commands中的LC_ENCRYPTION_INFO的Crypt ID 如果是0代表已脱壳或者未加密
	2.通过指令 otool -l 可执行文件路径 | grep crypt 来查看
	
##九.Clutch
1.安装地址： https://github.com/KJCracks/Clutch/releases
2.拷贝Clutch可执行工具放到 /usr/bin 目录下
3.Permission denied: chmod +x 可执行文件路径
4.常用命令

	1.列出已安装APP
		Clutch -i
	
	2.脱壳
		Clutch -d APP序号(或BundleId)	

##十.theos





	

