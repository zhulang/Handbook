## 一.手机越狱
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
	
## 二.Mac工具安装

1.iFunBox:用于在Mac端浏览iPhone文件系统

2.Reveal:用于析构APP页面

3.Hopper Disassembler:静态分析，可执行文件转汇编

4.MachOView:用于查看可执行文件的内存分布

5.Clutch:用于脱壳

## 三.OpenSSH远程登录

1.wifi环境

	(1)登录ssh root@192.168.0.199 (192.168.0.199iOS设备在局域网里的ip地址)
	(2)原始密码：alpine
	(3)修改密码：password 、password mobile
	(4)删掉之前链接的公钥信息：ssh-keygen -R 服务器IP地址 或 vim ~/.ssh/known_hosts
		
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
	
## 四.Cycript
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
	
## 五.class-dump
1.导出头文件

	class-dump  -H  Mach-O文件路径  -o  输出文件目录
	
## 六.Hopper Disassmbler
1.常用快捷键

	找出指定方法：Shift + Option + X
	
## 七.Mach-O文件
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

## 八.脱壳
1.工具:
	
	Clutch、dumpdecrypted、CrackerXI(我采用第三个工具)
2.是否脱壳

	1.查看Mach-O文件Load Commands中的LC_ENCRYPTION_INFO的Crypt ID 如果是0代表已脱壳或者未加密
	2.通过指令 otool -l 可执行文件路径 | grep crypt 来查看
	
## 九.Clutch
1.安装地址： https://github.com/KJCracks/Clutch/releases
2.拷贝Clutch可执行工具放到 /usr/bin 目录下
3.Permission denied: chmod +x 可执行文件路径
4.常用命令

	1.列出已安装APP
		Clutch -i
	
	2.脱壳
		Clutch -d APP序号(或BundleId)	

## 十.theos
theos是构建tweak的工具

1.新建tweak项目
	
	在theos安装后，命令行
		nic.pl


## 十一.动态调试


## 十二.签名机制
1.文件作用描述

		1.Mac设备的公钥:CertificateSigningRequest.certSigningRequest
		
		2.Apple developer后台下载.cer证书->{Mac设备的公钥 + Apple私钥 + 签名}
		
		3.{.cer证书+ devices + appid + entitlements} + Apple私钥 ->.mobileprovision文件
		
2.常用指令
 	
	1.codesign -f -s 证书ID --entitlements entitlements.plst APP名称.app
	注释：-f 强制 f和s可以连载一起，比如 -fs
	
3.签名💰准备好mobilepro
	

4.重签名

	1.下载mobileprovision文件命名为embedded
	2.将embedded.mobileprovision文件移动到 脱壳 后的 youappname.app包内，替换原来的embedded.mobileprovision文件
	3.替换后重签名
	  a.查找证书ID
	  	security find-identity -v -p codesigning
		
	   b.抽取entitlements文件执行以下两行
	   	security cms -D -i embedded.mobileprovision > temp.plist
		/usr/libexec/PlistBuddy -x -c 'Print :Entitlements' temp.plist > entitlements.plist
		
	   c.将entitlements.plist拖到Payload包中
	   
	   d.终端命令行cd到Payload目录中
	   
	   e.签名
	   	codesign -fs 证书ID --entitlements xxx.plist xxx.app
		
	   f.删除plist文件
	   
	   g.压缩Payload包
       
       4.查看加载的动态库
         otool -L Mach-O文件
         
       5.动态库注入 (insert_dylib) 
       
         a.下载地址：https://github.com/Tyilo/insert_dylib
         
         b. 将要注入的库拖到可执行文件中
         
         c.insert_dylib @executable_path/动态库名 可执行文件名 --all-yes --weak 新生成可执行文件名
         
         d.copy CydiaSubstrate、动态库文件以及embedded.mobileprovision放到.app中
         
         e.insall_name_tool -change /library/Frameworks/CydiaSubstrate.framework/CydiaSubstrate @loader_path/CydiaSubstrate 动态库文件
         
         f.命令行cd到.app包中，对动态库、CydiaSubstrate 重签名
         
         g.用signer对.app进行签名
         
5.解压deb


6.llvm
    
    1.查看编译文件经历的 几个阶段
        clang -ccc-print-phases main.m
        
    2.查看预编译结果
        clang -E main.m
        
    3.词法分析
        clang -fmodules -E -Xclang -dump-tokens main.m
    
    4.语法分析
        clang -fmodules -fsyntax-only -Xclang -ast-dump main.m
        
    5.查看AST树
    
    6.中间代码
        clang -S -emit-llvm main.m  //.ll
        clang -c -emit-llvm main.m  //.bc
    


7.lldb

    run or r: Launch a process

    step or s: Do a source level single step in the currently selected thread.

    next or n: Do a source level single step over in the currently selected thread.

    si :Do an instruction level single step in the currently selected thread

    ni Do an instruction level single step over in the currently selected thread.

    finish: Step out of the currently selected frame.

    continue or c: 继续运行

    thread until 12: Run until we hit line 12 or control leaves the current function.

    
       





	

