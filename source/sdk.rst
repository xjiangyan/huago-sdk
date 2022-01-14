华高扫描SDK 使用说明
====================





====================
概述
====================

..

	华高A4/A3高速扫描仪SDK为基于设备Android系统开发相关应用提供封装的API，同时sdk内部也内置了opencv的java库以方便对扫描后的图像进行二次开发。将相应.aar添加至Android项目中并依赖，就可以使用SDK中的功能。同时我们也提供了SDK使用的demo，以及生产的apk文件，以便体验和开发。

==============
常见扫描仪状态
==============

..

	状态获取方式：  **HGScanManager.getInstance().getState();**


=======================	================   ===============================================
   EventDef.STATE       内容               含义  
=======================	================   ===============================================
STATE_INIT               InitState         初始化
STATE_SDK_INIT           SdkInit           SDK初始化成功，可以获取扫描仪的信息以及执行操作
STATE_COVER_OPEN         CoverOpenState    扫描仪盖子打开
STATE_SCANNING           ScanningState     扫描状态
STATE_STANDBY            StandbyState      准备就绪（空闲）状态
STATE_PAPER_READY        PaperReadyState   检测到纸张就绪  
STATE_ERROR_JAM          ErrorJamState     异常（阻塞）状态
STATE_ERROR              ErrorState        错误状态
STATE_CAMERA             CameraState       摄像头开启
STATE_CREATE_CORRECT     CreateCorrect     校正作业中
STATE_DEVICE_REPARING    RepairingState    设备恢复中
=======================	================   ===============================================



===============
常见扫描仪信号
===============

==========================	================		========================
  EventDef.S_EVT			 code		    				含义
==========================	================		========================
S_EVT_COVER_OPENED      	1							扫描仪盖子打开
S_EVT_CLEAR_ERR				3							清除错误状态
S_EVT_PAPER_PULLOUT  		5							退出纸张
S_EVT_ERROR_JAM      		8							卡纸错误
S_EVT_ERROR_DOUBLEPAPER		16							双张错误
S_EVT_ERROR_FPGA      		68							FPGA错误
S_EVT_START_SCAN      		69							开始扫描
S_EVT_STOP_SCAN      		70							停止扫描
S_EVT_PAPER_NOT_STANDBY		96							纸张未就绪
S_EVT_PAPER_STANDBY			97							纸张就绪
S_EVT_COVER_CLOSED    		258							扫描仪盖子合起
S_EVT_DOUBLEPAPER      		272							检测到双张
S_EVT_SCAN_STARTED      	274							已经开始扫描
S_EVT_SCAN_STOPPED      	275							已经停止扫描
S_EVT_JAM_IN				276							搓纸失败
S_EVT_JAM_OUT				277							卡纸
S_EVT_SAMPLE_ERR      		278							图像采样错误
S_EVT_DEVICE_UNVAILABLE	 	520							设备不可用
S_EVT_DEVICE_VAILABLE		521							设备可用
S_EVT_WHEEL_NEED_REPLACED	528							滚轮需要替换
==========================	================		========================


===========
运行流程图
===========

.. image:: /image/HuagaoSDK_Image_0.png


==========================
运行时sdk发送的状态与信号
==========================


- **识别到纸张放置**

..

=================     ========
状态                  信号
=================     ========
PaperReadyState;      code:97
=================     ========

- **待机状态**

..

=================     ========
状态                  信号
=================     ========
StandbyState;         code:96
=================     ========

- **进入扫描状态**

==============	========       ============================
状态            信号              	       
==============	========       ============================
ScanningState;	code:69        S_EVT_START_SCAN = 69;
ScanningState;  code:274       S_EVT_SCAN_STARTED = 274;
==============	========       ============================

- **扫描完成**

================      =========     ==========================
状态                  信号           
================      =========     ==========================
StandbyState;         code:275      S_EVT_SCAN_STOPPED = 275; 
PaperReadyState;      code:70       S_EVT_STOP_SCAN = 70;
================      =========     ==========================

- **打开扫描仪盖子**

=======================       ========
状态                          信号
=======================       ========
status:CoverOpenState;        code:1
status:CoverOpenState;        code:96
status:CoverOpenState;        code:97
=======================       ========


- **关闭扫描仪盖子**

=====================       ========
状态                        信号
=====================       ========
status:StandbyState;        code:258
=====================       ========


- **当程序运行异常，卡纸，双张以及其他被定义为“异常”的规则**

=================     ========     =====================
状态                  信号
=================     ========     =====================
ErrorJamState;        code:272     S_EVT_DOUBLEPAPER = 272;
ErrorJamState;        code:276     S_EVT_JAM_IN = 276;
=================     ========     =====================



- **退出卡纸**

=================     ========  =========================
状态                  信号
=================     ========  =========================
ErrorJamState;        code:5    S_EVT_PAPER_PULLOUT = 5;
=================     ========  =========================






.. [#f1]无纸张时，开启扫描只会出现status:StandbyState;  code:69 并不会发出（回调）finish信号。

================
常用sdk功能方法
================

	**HGScanManager.getInstance().xxx();**

=============================================================			======================================================================================================================================================================================================================================================================
 方法名定义  															含义
=============================================================  			======================================================================================================================================================================================================================================================================
int init(Context var1);													sdk初始化
void setScanEventListener(ScanEventListener var1)  						扫描仪事件监听，参见{ScanEventListener}
void setPreviewCallback(PreviewCallback callback, int format)   		注册扫描仪图像预览回调接口（format:PreviewCallback.xx）
String getState()  														获取扫描仪当前状态
void setSetting(ScanSetting setting)  									设置扫描参数，参见{ScanSetting}
ScanSetting getScanSetting() 											获取当前扫描参数
void releasePreviewCallback()  											释放预览回调，避免context无法释放
void startScan()  														开始扫描(返回的index从1开始递增)
void resumeScan()  														继续扫描（返回的index从上一个继续递增）
void pauseScan() 														暂停扫描
void stopScan()  														停止扫描
void setScanMode(int mode)  											设置扫描模式  单面， 双面@param mode {@link ScanDef.ScanMode}
void setColorMode(int mode) 											设置颜色模式@param mode {@link ScanDef.ColorMode}
void setQuality(int quality)  											设置扫描质量@param mode {@link ScanDef.Qulality}
void setAutoCut(boolean autoCut)  										自动裁剪
void setAdjust(boolean adjust) 											自动纠偏
void setDoubleChecked(boolean b)  										是否开启双张检测（开启后检测到双张，扫描仪将停止扫描，此时需要把纸从设备中取出
void setSkipBlank(boolean b)  											是否跳过空白页（空白页是指纯色页面，文稿内无其他内容，不限于纯白纸）
void setFixCorner(boolean b)   											是否填充角落@since SDK 2.2.0
void setSavePath(String dir, String prefix) 							设置保存路径@param dir 要保存的文件夹@param prefix 文件名前缀
int getWheelPages()  													当前滚轮已扫描页数
void clearWheelPages()  												更换滚轮，已清除滚轮扫描页数
int getTotalPages()  													历史扫描总数
void setPaperHeight(String type, int  height) 							设置纸张高度 @param type:纸张型号@param height：单位mm   {@link Paper}
Bitmap getCachedPrevImage(int index)  									获取缓存的缩略图@param index 扫描图片索引 @return bitmap缩略图（仅限最新20张图像
void setAdjustOrientation(int type, boolean discardUnMarked)  			设置mark点文本校正方向 @param type mark类型，可选：{@link ScanDef.MarkType#Muti}、{@link ScanDef.MarkType#Tri}@param discardUnMarked 是否丢掉没有mark定位的图（需要纸张有特定的mark点标记才能把图片矫正为“正向”，具体点位需要与开发人员联系获取）
void setAdjustOrientation(boolean b)  									设置mark点文本校正方向 ，默认多边mark点，不丢弃图（需要纸张有特定的mark点标记才能把图片矫正为“正向”，具体点位需要与开发人员联系获取）
void setAutoScanDelayTime(int millisecond) 								设置多少毫秒内待纸自动扫描
void setWaitPaperTime													设置自动待纸扫描的等待时间
Statistics getStatistics()  											获取设备扫描数据统计
void flipPage(boolean flip)	 											AB面翻转（默认false：面向用户页先出图true:面向用户页后出图）
void reportInfo															设备日志上传(description：例如错误如何出现)
void getSN																设备唯一编号
void setCropThreshold													设置裁切时明暗场阈值(默认40)
void setDetectDogEar													折角检测
void setDpi																自定义dpi
=============================================================			======================================================================================================================================================================================================================================================================



=========
快速开始
=========

----------------------------
- **在build.gradle中配置**
----------------------------

::
	
	//统一使用'armeabi-v7a'
    ndk {
            abiFilters 'armeabi-v7a'
        }
	
----------------------------
- **在application中初始化**
----------------------------

::

	@Override
    public void onCreate() {
        super.onCreate();
        HGScanManager.getInstance().init(this);
        HGScanManager.getInstance().setScanEventListener(mScanEventListener);
    }

	//扫描仪事件监听
    private ScanEventListener mScanEventListener = new ScanEventListener() {
        @Override
        public void onEvent(final int code, final String status) {
            switch (status) {
                case EventDef.STATE_STANDBY:    //  空闲（没有触发‘有无纸弹片’）
                    break;
                case EventDef.STATE_PAPER_READY:  // 就绪（纸张放好）
                    break;
                case EventDef.STATE_COVER_OPEN: //  开盖
                    break;
                case EventDef.STATE_SCANNING:
                    if (code == EventDef.S_EVT_START_SCAN) { //开始扫描了
                    }
                    break;
                case EventDef.STATE_ERROR:
                case EventDef.STATE_ERROR_JAM://错误状态
                    break;
                default:
                    break;
            }
            if (code == EventDef.S_EVT_SCAN_STOPPED) {//扫描结束信号（指机械走纸已结束，即人眼看到的已经扫描完成。因为图像处理是异步的，所以延迟200~300毫秒左右，可以确保图像也处理完成并抛出）
            }
        }
    };

---------------
- **基本配置**
---------------


::

		HGScanManager.getInstance().setSavePath("/sdcard/picture", "Doc");//设置保存路径 prefix:文件名前缀
		HGScanManager.getInstance().setScanMode(ScanDef.ScanMode.Single);//单面扫描
		HGScanManager.getInstance().setColorMode(ScanDef.ColorMode.Color);//设置扫描模式--黑白/彩色
		HGScanManager.getInstance().setQuality(ScanDef.Qulality.Standard);//设置扫描图像质量 200dpi(标准)
		HGScanManager.getInstance().setDoubleChecked(true);//开启双张检测
		//关于图像裁切与图像纠偏功能
		//一般 setAutoCut 与 setAdjust 会同时开启，这样可以得到完整的切割好的图像。
		//关闭的话，会输出包含黑色背景的原图。当出现异常图像时，可以输出原图，给开发人员
		分析图像处理异常的原因
		HGScanManager.getInstance().setAutoCut(true);//开启图像裁切
		HGScanManager.getInstance().setAdjust(true);//开启图像纠偏
		//AB面翻转 @default false：面向用户页后出图 true:面向用户页先出图
		HGScanManager.getInstance().flipPage(false);



---------------
- **开启回调**
---------------

::

	// @params index:纸张出图顺序index，绝对值相同的为一张纸的正反面
	// @params image: 1.当保存格式为：
	// FORMAT_JPEG_FILE、FORMAT_TIFF_FILE 时：
	// 在本地的图片地址 例如：/sdcard/picture/Doc1630391378_5.jpg
	// 若文件名末尾数字的绝对值相同，属于一张纸的正反面。
	// 例如：Doc1630391378_5.jpg 与 Doc1630391390_-5.jpg。
	// FORMAT_BITMAP 时：image返回的是bitmap
	//（重复注册会清空图像队列，可能会导致上一次的图片没接收完，请确保在接收到扫描仪停止信号之后，再次注册。建议一个页面就注册一次，setPreviewCallback方法与releasePreviewCallback方法需要成对出现）
	HGScanManager.getInstance().setPreviewCallback(new PreviewCallback() {
		@Override
		public void onPreview(int index, Object image) {
		
			BitmapInfo bitmapInfo = new BitmapInfo();
			bitmapInfo.index = index;//index
			bitmapInfo.path = (String) image;//在本地的图片地址
			
	}}, PreviewCallback.FORMAT_JPEG_FILE);//设置保存格式为jpeg


--------------------
- **释放预览接口**
--------------------

::

	@Override
    protected void onDestroy() {
        //释放预览接口以避免可能的内存泄露
        HGScanManager.getInstance().releasePreviewCallback();
        super.onDestroy();
    }

---------------------------------------------
- **开始扫描（回调中index重置为1开始计数）**
---------------------------------------------

::

	HGScanManager.getInstance().startScan();  

----------------
- **暂停扫描**
----------------

::

	HGScanManager.getInstance().pauseScan();
	
---------------
- **停止扫描**
---------------

::

	HGScanManager.getInstance().stopScan();

--------------------------------------------------
- **重新扫描/继续扫描（回调中index会继续递增）**
--------------------------------------------------

::

	HGScanManager.getInstance().resumeScan();

---------------
- **退出双张**
---------------

::

	//当纸张歪斜的卡在设备中，此时使用该功能可能会使纸张受损，建议异常时弹窗提示手动开盖取出纸张并重新扫描
	HGScanManager.getInstance().operate(ScanDef.CMD.PULL_PAPER);//退出双张

---------------
- **扫描统计**
---------------

::

	Statistics statistics = HGScanManager.getInstance().getStatistics();//获取统计数据
	Statistics ：
		int totalPage; //扫描总页数
		int cuoNum;    //搓纸总数
		int jamInNum;  //搓纸失败次数
		int jamOutNum; //卡纸次数
		int doubleNum; //双张次数
		int errorNum;  //其他异常次数
		
=========
混淆规则
=========

::

	-keep class xcrash.** { *; }
	-keep class org.xutils.** { *; }
	-keep class org.opencv.** { *; }
	-keepattributes Signature
	-dontwarn com.alibaba.fastjson.**
	-keep class com.alibaba.fastjson.**{*; }
	-keep class com.huagaoscan.sdk.** { *; }

==========
版本变动
==========
---------------
- **2.5.6版本**
---------------

-------------------------
1.添加日志上报功能 
-------------------------

::

	//当设备出现故障，用户可上传本机信息进行后台分析
	//@param description 描述信息（可选） 填入 “问题产生的经过”，方便后续问题排查
	//@param callBack  日志上传回调
	// 此操作会默认上传设备最新的部分日志
	reportInfo(String description, LogcatManager.ReportCallBack callBack)

-----------------------------
2. 设置自动待纸扫描的等待时间
-----------------------------

::

	//@param time 单位：毫秒
	setWaitPaperTime(int time)

-------------------------
3.设置裁切时明暗场阈值
-------------------------

::

	//@param threshold 默认值为40
	setCropThreshold(int threshold)

---------------
- **2.6.0版本**
---------------


-------------------------
1.添加折角检测功能
-------------------------

::

	//@since SDK 2.6.0   @param b 是否检测折角
	//  检测到折角不会停止扫描，只会在图片回调中添加一个标志tag
	setDetectDogEar(boolean b);

-------------------------
2.添加新的图片回调方式
-------------------------

::

    HGScanManager.getInstance().setPreviewCallback(new PreviewWithRecCallback() {
            //开启了‘折角检测’ 等功能时回调,多一个检测结果
            //RecResult::hasDogEar 是否有折角
            @Override
            public void onPreview(int index, Object image, RecResult recResult) {
           
            }
            //普通回调，与2.6.0之前版本逻辑一致
            @Override
            public void onPreview(int index, Object image) {
           
            }
        }, PreviewCallback.FORMAT_JPEG_FILE);

	//对比sdk2.6.0之前的图片回调
	//HGScanManager.getInstance().setPreviewCallback(new PreviewCallback() {
	//    @Override
	//    public void onPreview(int index, Object image) {
	//        
	//    }
	//}, PreviewCallback.FORMAT_JPEG_FILE);


-------------------------
3.添加二维码识别功能
-------------------------

::

		//该方案是移植的微信在openCV开放的二维码功能，具体容错率等参数可以直接查看文档或自行百度：
		// https://docs.opencv.org/4.x/d5/d04/classcv_1_1wechat__qrcode_1_1WeChatQRCode.html
		// github地址：
		// https://github.com/opencv/opencv_contrib/tree/master/modules/wechat_qrcode
		// 打不开可以访问码云上的第三方地址：
		// https://gitee.com/idledo/opencv_contrib/tree/4.x/modules/wechat_qrcode
		  QrCodeDec qrCodeDec = new QrCodeDec();
		  
		  //传入bitmap进行检测  返回值：可能是多个二维码信息
		  List<String> decode = qrCodeDec.decode(bitmap);
		  //传入图片路径进行全图检测
		  List<String> decode = qrCodeDec.decode(path);
		  //传入图片路径，并传入要检测的矩形区域   
		  //org.opencv.core.Rect(0,0,60,60)：左上角坐标为0,0  并且宽高都为60的矩形
		  List<String> decode = qrCodeDec.decode(path, new org.opencv.core.Rect(0,0,60,60));




---------------
- **2.6.1版本**
---------------

-------------------------
1.新增自定义dpi功能
-------------------------

::

	//建议取值范围在150-600之间，取值过低图像质量低；取值过高，图像处理时间
	增长，甚至可能会出现内存溢出的情况。
	HGScanManager.getInstance().setDpi(200);
	//对比2.6.1之前的dpi设置：
	// ScanDef.Qulality.Low（150dpi）
	// ScanDef.Qulality.Standard(200dpi)
	// ScanDef.Qulality.High(300dpi)
	HGScanManager.getInstance().setQuality(ScanDef.Qulality.Stand
	ard)


=============
常见问题
=============

-------------------------
1、如何使用adb连接设备？
-------------------------

..

	设备连接局域网后，在命令行中输入adb connnect 设备ip:5555即可。（rk3288版本不可使用usb调试）

-----------------------------------------
2、扫描纸张的时候越扫越慢，这是什么原因？
-----------------------------------------
..

	我们是扫描一体机，不同于传统机器图片处理放在PC端处理，所以在处理图片的时候会根据任务对速度有个调节，最终的速度会是稳定的。如果在本机做过多图像处理等导致CPU使用率增加而降低了扫描速度的情况，建议将重量级任务放到云端处理，这样设备可以达到较快扫描速度。

--------------------------------------	
3、如何查看设备信息，如固件版本等？
--------------------------------------

..

	打开高级设置App，在关于中可以查看。


.. image:: /image/HuagaoSDK_Image_1.jpg


.. image:: /image/HuagaoSDK_Image_2.jpg

---------------------------------
4、如何配置自己的launcher程序？
---------------------------------

..

	1:首先在AndroidManifest.xml中，确保存在 category.DEFAULT 以及category.HOME

::

	<activity android:name=".MainActivity">
		<intent-filter>
			<action android:name="android.intent.action.MAIN"/>
			<category android:name="android.intent.category.DEFAULT" /><!--必须-->
			<category android:name="android.intent.category.HOME"></category><!--必须-->
			<category android:name="android.intent.category.LAUNCHER" />
		</intent-filter>
	</activity>

..

	2:进入adb shell之后，setprop persist.sys.default.home “定制launcher的包名”，之后设备重启完成可以将系统启动桌面设置为自己开发的launcher
	
----------------------
5、如何隐藏虚拟按键？
----------------------
..

	进入adb shell之后，setprop persist.sys.hidenav 1,可以隐藏虚拟按键，全屏显示 设为0则显示虚拟按键
	
-----------------------------
6、如何开机自动拉起一个应用？
-----------------------------
..

	1 : 首先在AndroidManifest.xml中，确保存在category.DEFAULT

::

	<activity android:name=".MainActivity">
		<intent-filter>
			<action android:name="android.intent.action.MAIN"/>
			<category android:name="android.intent.category.DEFAULT" /><!--必须-->
			<category android:name="android.intent.category.LAUNCHER" />
		</intent-filter>
	</activity>
	
..

	进入adb shell之后，setprop persist.sys.package.autorun “应用包名”。
	
---------------------------------------------------------------
7、在设置了扫描回调之后，扫描仪扫描结束为什么不会返回图片信息？
---------------------------------------------------------------
..

	请确保只开启了一个扫描程序，后开启监听的扫描程序会优先接收到扫描信息，而之前的不会接收消息。

---------------------------------------------------------------
8、关于设备清洁维护
---------------------------------------------------------------
..
	当搓纸轮已超过15万次搓纸，扫描作业过程中搓纸失败、歪斜、搓多张等异常频次可能会明显增多，建议及时弹窗提示用户替换纸轮。

::
	
	//在应用中可以调用该方法判断当前搓纸轮是否需要更换。
	HGScanManager.getInstance().isWheelNeedReplaced()
	//调用该方法获取当前搓纸轮具体搓纸次数。
	HGScanManager.getInstance().getWheelPages()
	//在更换后调用该方法清空设备中存储的搓纸次数。
	HGScanManager.getInstance().clearWheelPages()





















