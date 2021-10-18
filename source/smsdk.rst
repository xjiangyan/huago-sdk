华高SYS-SDK 使用说明
====================


====================
概述
====================

..

	本SYS-SDK(SystemSdk)对基于rk3399版本的Android系统的华高扫描仪，提供一些常用功能以及系统性功能帮助Android开发。

================
sdk调用方式
================

	**HGSM.getInstance().xxx();**

=============================================================			==============================================================================================================================================================
 方法名定义  															含义
=============================================================  			==============================================================================================================================================================
String getKernelVersion()												获取kernel版本信息
String getAndroidVersion()												获取Android版本信息
String getFirmwareVersion()   											获取固件版本信息
String getAPIVersion()  												获取api版本信息
String getAndroidDisplay() 												获取系统安装（升级）时间
void hideNavBar(boolean hide) 											设置是否隐藏底部导航栏
void setStatusBarDragable(boolean  drag)  								设置是否可以下拉出顶部状态栏
void setAppAutoRun(String pkg) 											设置开机需要拉起的app
void setDefaultLauncher(String pkg) 									设置默认的launcher
List<RemovableStorageInfo> getRemovableExternalStorageList() 			获取外部可移动存储列表（U盘等）
boolean unMountExternalStorage(String id)								根据提供的外部存储id来弹出设备
void shutdown()															关机
void reboot(boolean safeMode)											重启
void togglePanel()														拉出\收起状态栏
void collapsePanels()													收起状态栏
void expandPanel()														展开状态栏
void setSysSleepTime(int delayTime)										设置系统的休眠时间
int getSleepTimeOut(Context context)									获取系统休眠时间
int getScreenWidth(Context context)										获取屏幕可用宽度px
int getScreenHeight(Context context)									获取屏幕可用高度px
int getFullScreenWidth(Activity context)								获取全屏的宽度px
int getFullScreenHeight(Activity context)								获取全屏的高度px
int getDensityDpi(Context context)										获取屏幕密度值
boolean setDensityDpi(int density)										设置屏幕密度
boolean resetDensityDpi()												重置屏幕密度
boolean takeScreenshot(String path, String name)						屏幕截屏
boolean setScreenOff(boolean off)										控制屏幕开关
boolean clearApp(String pkg)											清除应用缓存
boolean forceStopApp(String pkg)										强制关闭某个应用
boolean execSuCmd(String command)										以root权限执行shell命令
=============================================================			==============================================================================================================================================================



=========
方法说明
=========

----------------------------
- **获取kernel版本信息**
----------------------------

::

	HGSM.getInstance().getKernelVersion(); 

-----------------------------
- **获取Android系统版本信息**
-----------------------------

::

	HGSM.getInstance().getAndroidVersion(); 

----------------------
- **获取固件版本信息**
----------------------

::

	HGSM.getInstance().getFirmwareVersion(); 

---------------------
- **获取api版本信息**
---------------------

::

	HGSM.getInstance().getAPIVersion(); 

------------------------------
- **获取系统安装（升级）时间**
------------------------------

::

	HGSM.getInstance().getAndroidDisplay(); 

----------------------------
- **设置是否隐藏底部导航栏**
----------------------------

::

	//true:隐藏底部导航栏 false:显示底部导航栏 
	HGSM.getInstance().hideNavBar(true);

------------------------------------
- **设置是否可以上下拉出顶部状态栏**
------------------------------------

::

	//true:允许下拉显示状态栏 false:禁止下拉显示状态栏 
	HGSM.getInstance().setStatusBarDragable(true);
	
----------------------------------
- **设置开机需要拉起的app**
----------------------------------

::

	//@param pkg  需要开机启动的apk包名
	HGSM.getInstance().setAppAutoRun("com.huago.app");
	
----------------------------------
- **设置默认的launcher**
----------------------------------

::

	//@param pkg  需要设为默认launcher的apk包名
	HGSM.getInstance().setDefaultLauncher("com.huago.app");
	
----------------------------------------------
- **获取已挂载的外部可移动存储列表（U盘等）**
----------------------------------------------

::

	//@return RemovableStorageInfo 包含有path（装载的路径）以及id（装载的id）
	// storageList.getPath():外部存储设备在扫描仪上挂载的根路径，可以添加子路径来实现外部存储的写入\读取文件功能
	// storageList.getId(): 在弹出外部存储设备时可用
	List<RemovableStorageInfo> storageList = HGSM.getInstance().getRemovableExternalStorageList();
	
-------------------------------------
- **根据提供的外部存储id来弹出设备**
-------------------------------------

::

	//@param id 由getRemovableExternalStorageList方法获取到的id
	//@return result: true:弹出成功  false:弹出失败  弹出后即可拔出外部存储（直接拔出可能导致损坏）
	//弹出所需时间与存储设备本身有关，可能比较耗时
	boolean b = HGSM.getInstance().unMountExternalStorage(id);

-------------------------------------
- **关机**
-------------------------------------

::

	HGSM.getInstance().shutdown();

-------------------------------------
- **重启**
-------------------------------------

::

	//@param safeMode 是否重启进入安全模式  true:重启并进入安全模式  false:单纯重启
	HGSM.getInstance().reboot(safeMode);

-------------------------------------
- **拉出/收起状态栏**
-------------------------------------

::

	//当前是拉出状态栏时，将收起，反之亦然
	HGSM.getInstance().togglePanel();

-------------------------------------
- **收起状态栏**
-------------------------------------

::

	HGSM.getInstance().collapsePanels();

-------------------------------------
- **展开状态栏**
-------------------------------------

::

	HGSM.getInstance().expandPanel();

-------------------------------------
- **设置系统的休眠时间**
-------------------------------------

::

	//@param delayTime 屏幕将在delayTime毫秒之后息屏。传入参数不得少于 30*1000（30秒）
	boolean result = HGSM.getInstance().setSysSleepTime(delayTime);                       

-------------------------------------
- **获取系统的休眠时间**
-------------------------------------

::

	//@param content 上下文  
	//@return 毫秒
	int time = HGSM.getInstance().getSleepTimeOut(content);

-------------------------------------
- **获取屏幕可用高度px**
-------------------------------------

::

	//@param content 上下文  
	//@return 高度px 可能去除了状态栏\导航栏的高度
	int height = HGSM.getInstance().getScreenHeight(content);

-------------------------------------
- **获取屏幕可用宽度px**
-------------------------------------

::

	//@param content 上下文  
	//@return 宽度px 可能去除了状态栏\导航栏的宽度
	int width = HGSM.getInstance().getScreenWidth(content);

-------------------------------------
- **获取全屏的宽度px**
-------------------------------------

::

	//@param activity   
	//@return 宽度px 
	int width = HGSM.getInstance().getFullScreenWidth(activity);

-------------------------------------
- **获取全屏的高度px**
-------------------------------------

::

	//@param activity   
	//@return 高度px 
	int height = HGSM.getInstance().getFullScreenHeight(activity);

-------------------------------------
- **获取屏幕密度值**
-------------------------------------

::

	//@param content 上下文  
	//@return 屏幕密度dpi 
	int density = HGSM.getInstance().getDensityDpi(content);

-------------------------------------
- **设置屏幕密度**
-------------------------------------

::

	//@param density dpi 取值范围 160~600  
	HGSM.getInstance().setDensityDpi(density);

-------------------------------------
- **重置屏幕密度**
-------------------------------------

::
 
	HGSM.getInstance().resetDensityDpi();

-------------------------------------
- **屏幕截屏**
-------------------------------------

::

	//@param path 需要保存的目录  需要保证路径存在
	//@param name 保存文件名
	HGSM.getInstance().takeScreenshot(String path, String name);

-------------------------------------
- **控制屏幕开关**
-------------------------------------

::

	//@param off  true:息屏 false:亮屏
	HGSM.getInstance().setScreenOff(true);

-------------------------------------
- **清除应用缓存**
-------------------------------------

::

	//效果相当于在设置里的应用信息界面点击了「清除缓存」和「清除数据」
	//可用于恢复一些异常状态
	//@param pkg 要清除应用缓存的APP包名
	HGSM.getInstance().clearApp("com.huago.app");

-------------------------------------
- **强制关闭某个应用**
-------------------------------------

::

	//@param pkg  要关闭的APP包名
	HGSM.getInstance().forceStopApp("com.huago.app");
	
-------------------------------------
- **以root权限执行命令**
-------------------------------------

::

	//@param command  要执行的命令 可能有些命令不支持
	HGSM.getInstance().execSuCmd(command);


===============
混淆规则
===============

::

	-keep class com.huagao.sm.** { *; }
	-keep class com.android.internal.statusbar.** { *; }














