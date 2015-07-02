# 5 WMS
全局的窗口管理
全局的事件管理派发
	WMS管理的事件源
		键盘 Home键 Back键
		触摸屏
		鼠标 不常见
		轨迹球
窗口管理器所涉及的元素最少包括以下几个
1 窗口管理员
1 WMS
	由ServerThread负责启动
	直到系统关机才退出
	发生异常必须能自动重启
2 SurfaceFinger

3 有图形显示需求的程序
	Application Window //针对普通应用程序的显示申请所产生的窗口
	System Window //顶部的系统状态栏 壁纸
	Sub Window //子窗口

4 InputManagerService
	派发系统按键和触摸消息
5 ActivityManagerService
	例如两个界面之间的切换
6 Binder通信

WindowManagerService包含的子功能
	窗口的添加和删除
	启动窗口
	窗口动画
	窗口大小
	窗口层级
	事件派发
	
2 窗口属性
	Flags 窗口的标志
		public static final int FLAG_ALLOW_LOCK_WHILE_SCREEN_ON     = 0x00000001; //只要此窗口可见

        public static final int FLAG_DIM_BEHIND        = 0x00000002; //窗口后面的东西将变暗淡

        public static final int FLAG_BLUR_BEHIND        = 0x00000004; 

        public static final int FLAG_NOT_FOCUSABLE      = 0x00000008;//此窗口不获得输入焦点
        
        public static final int FLAG_NOT_TOUCHABLE      = 0x00000010; //此窗口不接受任何输入事件
        
        public static final int FLAG_NOT_TOUCH_MODAL    = 0x00000020; //无模式窗口
        
        public static final int FLAG_TOUCHABLE_WHEN_WAKING = 0x00000040; //设备睡眠 获得第一次触摸事件
        
        public static final int FLAG_KEEP_SCREEN_ON     = 0x00000080; //窗口可见 屏幕就亮着
        
        public static final int FLAG_LAYOUT_IN_SCREEN   = 0x00000100; //不考虑系统装饰窗
		
        public static final int FLAG_LAYOUT_NO_LIMITS   = 0x00000200; //窗口可以超过屏幕区域
        
        public static final int FLAG_FULLSCREEN      = 0x00000400; //隐藏所有屏幕装饰窗口
        
        public static final int FLAG_FORCE_NOT_FULLSCREEN   = 0x00000800;
        
        public static final int FLAG_DITHER             = 0x00001000;
        
        public static final int FLAG_SECURE             = 0x00002000; //窗口被认为是保密的
        
        public static final int FLAG_SCALED             = 0x00004000; //按照用户提供的参数做相应的伸缩调整
        
        public static final int FLAG_IGNORE_CHEEK_PRESSES    = 0x00008000; 
        
        public static final int FLAG_LAYOUT_INSET_DECOR = 0x00010000;
		
        public static final int FLAG_ALT_FOCUSABLE_IM = 0x00020000;
        
        public static final int FLAG_WATCH_OUTSIDE_TOUCH = 0x00040000;
        
        public static final int FLAG_SHOW_WHEN_LOCKED = 0x00080000; //窗口能在锁屏窗口上显示

        public static final int FLAG_SHOW_WALLPAPER = 0x00100000; //让壁纸在这个窗口显示
        
        public static final int FLAG_TURN_SCREEN_ON = 0x00200000; //窗口显示时将屏幕点亮
        
        public static final int FLAG_DISMISS_KEYGUARD = 0x00400000; //解除屏幕锁
        
        public static final int FLAG_SPLIT_TOUCH = 0x00800000;
        
        public static final int FLAG_HARDWARE_ACCELERATED = 0x01000000; //硬件加速

        public static final int FLAG_LAYOUT_IN_OVERSCAN = 0x02000000;

        public static final int FLAG_TRANSLUCENT_STATUS = 0x04000000;

        public static final int FLAG_TRANSLUCENT_NAVIGATION = 0x08000000;

        public static final int FLAG_LOCAL_FOCUS_MODE = 0x10000000;

        public static final int FLAG_SLIPPERY = 0x20000000;
		
        public static final int FLAG_NEEDS_MENU_KEY = 0x40000000;

3 窗口的添加过程
	
4 Surface管理

5 performLayoutAndPlaceSurfacesLocked
private final void performLayoutAndPlaceSurfacesLocked() {
        int loopCount = 6;
        do {
            mTraversalScheduled = false;
            performLayoutAndPlaceSurfacesLockedLoop();
            mH.removeMessages(H.DO_TRAVERSAL);
            loopCount--;
        } while (mTraversalScheduled && loopCount > 0);
        mInnerFields.mWallpaperActionPending = false;
    }
