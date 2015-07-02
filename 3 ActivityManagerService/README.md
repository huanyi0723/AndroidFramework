# 3 ActivityManagerService
AMS提供一个用于管理Activity运行状态的系统进程
ActivityStack
ActivityTask

1 概述

2 ActivityStack
enum ActivityState {
        INITIALIZING, //初始化
        RESUMED, //恢复
        PAUSING, //正在暂停
        PAUSED, //已经暂停
        STOPPING, //正在停止
        STOPPED, //已经停止
        FINISHING, //正在完成
        DESTROYING, //正在销毁
        DESTROYED //已经销毁
    }
	
记录信息
	mTaskHistory
	mValidateAppTokens
	mLRUActivities //正在运行的Activity的列表集合 以最近的使用情况排序
	mNoAnimActivities //列表中Activity 不考虑状态间迁移动画
	
	特殊状态下Activity
	mPausingActivity //正在被暂停
	mLastPausedActivity //上一个被暂停
	mLastNoHistoryActivity
	mResumedActivity //当前被恢复
	mLastStartedActivity //最近一次被启动

AMS通过ActivityStack来记录 管理系统中的Activity状态 并提供一个查询功能的系统服务

4 ActivityTask
管理ActivityTask的两种方式
	在<activity>标签中指定属性
		android:taskAffinity
		android:launchMode //启动方式 最终被启动的Activity通常位于ActivityTask的栈顶
			standard //默认状态 多实例 归属于startActivity将其启动的那个栈
			singleTop //多实例 但目标Activity位于栈顶时 不会生成
			singleTask //单实例 总在新的task中启动
			singleInstace //与singleTask一致 不过它永远在一个孤立的task中
	使用Intent标志
		

	
	