华高SDK 使用说明
================





====
概述
====

..

	华高A4高速扫描仪SDK为基于设备Android系统开发相关应用提供封装的API，同时sdk内部也内置了opencv的java库以方便对扫描后的图像进行二次开发。将HGSDK_x.x.x.aar添加至Android项目中并依赖，就可以使用SDK中的功能。同时我们也提供了SDK使用的demo，以及生产的apk文件，以便体验和开发。

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
S_EVT_SCAN_FINISHED   		0							扫描初始化状态正常
S_EVT_SCAN_FINISHED    		2							扫描结束
S_EVT_CLEAR_ERR				3							清除错误状态
S_EVT_PAPER_PULLOUT  		5							退出纸张
S_EVT_COVER_OPENED      	1							扫描仪盖子打开
S_EVT_COVER_CLOSED    		258							扫描仪盖子合起
S_EVT_PAPER_AT_SCAN     	8							正在扫描
S_EVT_PAPER_NOT_AT_SCAN		262							没有在扫描
S_EVT_DOUBLEPAPER      		272							检测到双张
S_EVT_NOT_DOUBLEPAPER      	273							检测到不是双张
S_EVT_START_SCAN      		69							开始扫描
S_EVT_STOP_SCAN      		70							停止扫描
S_EVT_SCAN_STARTED      	274							已经开始扫描
S_EVT_SCAN_STOPPED      	275							已经停止扫描
S_EVT_JAM_IN				276							搓纸失败/卡纸事件
S_EVT_JAM_OUT				277							卡纸/卡纸取出
S_EVT_SAMPLE_ERR      		278							图像采样错误
S_EVT_ERROR_PICKING      	4							取纸错误
S_EVT_ERROR_JAM      		8							卡纸错误
S_EVT_ERROR_DOUBLEPAPER		16							双张错误
S_EVT_ERROR_STAPLE      	32							unused
S_EVT_ERROR_FPGA      		68							FPGA错误
S_EVT_HAVING_IMAGE      	71							有图了
S_EVT_PAPER_NOT_STANDBY		0							不在待扫描状态
S_EVT_PAPER_STANDBY			0							处于待扫描状态
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

=============================================================			==============================================================================================================================================================
 方法名定义  															含义
=============================================================  			==============================================================================================================================================================
int init(Context var1);  sdk初始化
void setScanEventListener(ScanEventListener var1)  						扫描仪事件监听，参见{ScanEventListener}
void setPreviewCallback(PreviewCallback callback, int format)   		注册扫描仪图像预览回调接口（format:PreviewCallback.xx）
String getState()  														获取扫描仪当前状态
void setSetting(ScanSetting setting)  									设置扫描参数，参见{ScanSetting}
ScanSetting getScanSetting() 											获取当前扫描参数
void set(int attribute, String value)  									扫描仪参数设置 attribute 参见{ScanDef.Attribute} value：例如{ScanDef.ColorMode}
void set(int attribute, int value)  									扫描仪参数设置 attribute 参见{ScanDef.Attribute} value：例如{ScanDef.ColorMode}
void operate(int cmd)  													扫描仪操作，操作指令，参见{ScanDef.CMD}
void releasePreviewCallback()  											释放预览回调，避免context无法释放
void startScan()  														开始扫描
void resumeScan()  														继续扫描
void pauseScan() 														暂停扫描
void stopScan()  														停止扫描
void clearError()  														清除错误
void setScanMode(int mode)  											设置扫描模式  单面， 双面@param mode {@link ScanDef.ScanMode}
void setColorMode(int mode) 											设置颜色模式@param mode {@link ScanDef.ColorMode}
void setQuality(int quality)  											设置扫描质量@param mode {@link ScanDef.Qulality}
void setAutoCut(boolean autoCut)  										自动裁剪
void setAdjust(boolean adjust) 											自动纠偏
void setDoubleChecked(boolean b)  										是否开启双张检测
void setSkipBlank(boolean b)  											是否跳过空白页
void setFixCorner(boolean b)   											是否填充角落@since SDK 2.2.0
 void  setSavePath(String dir, String prefix) 							设置保存路径@param dir 要保存的文件夹@param prefix 文件名前缀
int getWheelPages()  													当前滚轮已扫描页数
void clearWheelPages()  												更换滚轮，已清除滚轮扫描页数
int getTotalPages()  													历史扫描总数
void setPaperHeight(String type, int  height) 							设置纸张高度 @param type:纸张型号@param height：单位mm   {@link Paper}
Bitmap getCachedPrevImage(int index)  									获取缓存的缩略图@param index 扫描图片索引 @return bitmap缩略图
void setAdjustOrientation(int type, boolean discardUnMarked)  			设置mark点文本校正方向 type：ScanDef.MarkType discardUnMarked：是否丢掉没有mark定位的图
void setAdjustOrientation(boolean b)  									设置mark点文本校正方向 ，默认多边mark点，不丢弃图
void setAutoScanDelayTime(int millisecond) 								设置多少毫秒内待纸自动扫描
Statistics getStatistics()  											获取设备扫描数据统计
void flipPage(boolean flip)	 											AB面翻转
=============================================================			==============================================================================================================================================================



=========
快速开始
=========

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
                case EventDef.STATE_SDK_INIT:  //SDK初始化 成功 可以获取扫描仪的信息以及执行操作了
                    break;
                case EventDef.STATE_STANDBY:    // 空闲
                    break;
                case EventDef.STATE_PAPER_READY:  // 就绪（纸张放好）
                    break;
                case EventDef.STATE_COVER_OPEN: // 纸盒打开
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
            if (code == EventDef.S_EVT_SCAN_STOPPED) {//扫描结束信号
            }
        }
    };

---------------
- **基本配置**
---------------


::

  /**
     * 设置保存路径
     * @param dir 要保存的文件夹
     * @param prefix 文件名前缀
     */
	HGScanManager.getInstance().setSavePath("/sdcard/picture", "Doc");
	HGScanManager.getInstance().setScanMode(ScanDef.ScanMode.Single);//单面打印
	HGScanManager.getInstance().setColorMode(ScanDef.ColorMode.Color);//设置打印模式--黑白/彩色
	HGScanManager.getInstance().setQuality(ScanDef.Qulality.High);//设置打印图像质量 300dpi(高)
	HGScanManager.getInstance().setDoubleChecked(true);//开启双张检测
	HGScanManager.getInstance().setAutoCut(true);//开启图像裁切
	HGScanManager.getInstance().setAdjust(true);//开启图像纠偏
	//开启mark点方向矫正,多边定位，不丢弃图
	HGScanManager.getInstance().setAdjustOrientation( ScanDef.MarkType.Muti, false)
	//HGScanManager.getInstance().setAdjustOrientation(true);//开启mark点方向矫正（默认多边定位，不丢弃图）

	//AB面翻转 @default false：面向用户页后出图   true:面向用户页先出图
	HGScanManager.getInstance().flipPage(false);


---------------
- **开启回调**
---------------

::

	HGScanManager.getInstance().setPreviewCallback(new PreviewCallback() {
            @Override
            public void onPreview(int index, Object image) {
                BitmapInfo bitmapInfo = new BitmapInfo();
                bitmapInfo.index = index;//index
                bitmapInfo.path = (String) image;//在本地的图片地址
            }
        }, PreviewCallback.FORMAT_JPEG_FILE);//设置保存格式为jpeg

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

------------------------
- **获取缓存的缩略图**
------------------------

::

	//index为previewCallback中返回的index 默认存储20张，可能为空
	Bitmap bitmap = HGScanManager.getInstance().getCachedPrevImage(index);

----------------
- **开始扫描**
----------------

::

	HGScanManager.getInstance().startScan();  

----------------
- **暂停扫描**
----------------

::

	HGScanManager.getInstance().operate(ScanDef.CMD.PASUE);
	//或
	HGScanManager.getInstance().pauseScan();
	
---------------
- **停止扫描**
---------------

::

	HGScanManager.getInstance().operate(ScanDef.CMD.STOP);
	//或
	HGScanManager.getInstance().stopScan();

---------------
- **重新扫描**
---------------

::

	HGScanManager.getInstance().operate(ScanDef.CMD.RESUME);
	//或
	HGScanManager.getInstance().resumeScan();

-----------------
- **异常处理**
-----------------

::

	HGScanManager.getInstance().operate(ScanDef.CMD.CLEAR_ERROR);//清除错误
	HGScanManager.getInstance().operate(ScanDef.CMD.RESET);//设备复位

---------------
- **退出卡纸**
---------------

::

	HGScanManager.getInstance().operate(ScanDef.CMD.PULL_PAPER);//退出卡纸

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

=============
常见问题
=============

-------------------------
1、如何使用adb连接设备？
-------------------------

..

	设备连接局域网后，在命令行中输入adb connnect 设备ip:5555即可。

-----------------------------------------
2、扫描纸张的时候越扫越慢，这是什么原因？
-----------------------------------------
..

	我们是扫描一体机，不同于传统机器图片处理放在PC端处理，所以在处理图片的时候会根据任务对速度有个调节，最终的速度会是稳定的。

--------------------------------------	
3、如何查看设备信息，如固件版本等？
--------------------------------------

..

	打开高级设置App，在关于中可以查看。


.. image:: /image/HuagaoSDK_Image_1.jpg


.. image:: /image/HuagaoSDK_Image_2.jpg

--------------------------------------------
4、为何运行sdk时会出现NoClassDefFound异常？
--------------------------------------------
..   

	sdk内部依赖三个库文件，请确保主工程已经依赖这两个库。


.. image:: /image/HuagaoSDK_Image_3.png

---------------------------------
5、如何配置自己的launcher程序？
---------------------------------
..

	进入adb shell之后，setprop persist.sys.default.home "定制launcher的包名"，可以将系统启动桌面设置为自己开发的launcher

----------------------
6、如何隐藏虚拟按键？
----------------------
..

	进入adb shell之后，setprop persist.sys.hidenav 1,可以隐藏虚拟按键，全屏显示





















