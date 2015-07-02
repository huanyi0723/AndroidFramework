# 6 View体系
1 应用程序中的View框架
Activity是应用程序各组件使用率最高 专门设计用于UI界面的显示和处理
	View和ViewRoot
		ViewRoot的核心任务是与WindowManagerService进行通信
	Activity和Window的关系
		private Window mWindow;
		Window是基类 根据不同的产品可以产生不同的子类 目前默认都是PhoneWindow
	Window与WindowManagerImpl的关系
		WindowManager管理多个Window
		WindowManager接口类 实现类是WindowManagerImpl

2 ViewTree的创建过程

3 在WMS中注册窗口

7 View与ViewGroup的属性
View中与UI相关的属性
	Identifier //View对象可以分配一个独特的id 以便使用者查询
		setId()
		findViewById()
		android:id
	Tag //记录与View有关的信息
		getTag()
		findViewWithTag()
		android:tag
	Position //View相对于父对象的四个边距 通常由系统计算
	Size //View对象的意愿大小
	Padding //View内部的填充 即内容区域与外边框的距离
	Margin //View不提供
	Gravity //重心位置
	Visibility //View的可见性
	ScrollBar //
	Background
	Effect //特效
	Transform //坐标变换
ViewGroup属性
		Margin
		Layout

	