# 2 进程间通信 Binder
Binder驱动 路由器
Service Manager DNS
Binder Client 客户端
Binder Server 服务器

进程1(客户端)希望与进程2(服务器)进行互访
但因为是跨进程的 必须借助Binder驱动来把请求正确投递到对方所在进程中

1 智能指针

2 进程间数据传递载体 Parcel
将对象相关数据打包
Parcel类相关方法
	1 设置相关
	dataSize() //获取当前已存储的数据大小
	setDataCapacity(int size) //设置Parcel空间大小
	setDataPosition(int pos) //改变Parcel的读写位置 0到dataSize()间
	dataAvail() //当前Parcel可读数据大小
	dataPosition() //数据当前位置值 类似游标
	dataCapacity() //当前Parcel的存储能力
	
	2 原始数据的读写操作
	writeByte(byte val)
	readByte()
	writeDouble(double val)
	readDouble()
	
	3 原始类型数组
	writeByteArray(byte[] b)
	writeByteArray(byte[] b, int offset, int len)
	createByteArray()
	readByteArray(byte[] val)
	
	4 遵循Parcelable协议的对象 例如Bundle
	writeParcelable(Parcelable p, int parcelableFlags) //将这个类的名字和内容写入Parcel中 实际上回调此Parcelable的writeToParcel()
	readParcelable(ClassLoader loader)
	writeParcelableArray(T[] value, int parcelableFlags) //写入Parcelable对象数组
	readParcelableArray(ClassLoader loader)
	
	5 Bundle
	Bundle的特点是使用键值对存储数据
	writeBundle(Bundle val)
	readBundle()
	readBundle(ClassLoader loader)
	
	6 Active Objects
	通常存入Parcel的是对象的内容 而Active Objects写入的则是它们的特殊标志引用
	两类对象
	Binder
	FileDescriptor
	
	7 java容器
	writeList(List val)
	writeArray(Object[] val)
	readArray(ClassLoader loader)
	readList(List outVal, ClassLoader loader)
	
3 Binder驱动和协议
Binder驱动运行于内核态 可以提供open() ioctl() mmap()等常用文件操作

4 Service Manager

5 Binder Client
	Activity
		startActivity()
	Service
		startService()
		bindService()
	Broadcast
		sendBroadcast
应用程序如何依托bindService来启动其它进程提供的Service
	1 应用程序填写Intent 调用bindService发出请求
	2 收到请求的bindService将与AMS取得联系
	3 AMS基于特定的最优匹配策略 从其内部存储的系统所有服务组件的资料中找出与Intent最匹配的一个
	4 被绑定的服务进程需要响应绑定 执行具体操作 成功完成后通知AMS
	
6 Android接口描述语言 AIDL

	
	
	