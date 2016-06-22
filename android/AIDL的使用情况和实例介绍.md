#AIDL是什么？

　　AIDL （Android Interface Definition Language）， Android接口定义语言，Android提供的IPC （Inter Process Communication，进程间通信）的一种独特实现。

#什么情况下要使用AIDL


　　使用AIDL只有在你允许来自不同应用的客户端跨进程通信访问你的service，并且想要在你的service种处理**多线程**的时候才是必要的。 如果你不需要执行不同应用之间的IPC并发，你应该通过实现Binder建立你的接口，或者如果你想执行IPC，但是不需要处理多线程。那么使用Messenger实现你的接口。

#定义一个AIDL接口的步骤

　　必须在一个.aidl文件中使用java编程语言语法定义你的AIDL接口，然后在提供service的应用中和任何绑定到这个service的应用中的源代码中（在src目录吓）保存它。
　　
　　当你编译包含.aidl文件的应用时，Android SDK工具基于这个.aidl文件生成一个IBinder接口，并且把它保存到项目的gen目录吓．service必须恰当的实现这个IBinder接口 之后客户端应用可以绑定到这个服务上，然后从IBinder调用方法来执行IPC。

使用AIDL建立一个邻接的service需要遵循下面的步骤：

1.　建立.aidl文件　

　　这个文件使用方法签名定义了语言接口　
　　
2.实现这个接口　

　　Android SDk工具基于你的.aidl文件使用java语言生成一个接口 这个接口有一个内部抽象类，叫做Stub，它是继承Binder并且实现你AIDL接口的 你必须继承这个Stub类并且实现这些方法
　　
3.暴露这个接口给客户端　

　　实现一个service并且覆盖onBind()方法返回你的Stub实现类。

> 你的.aidl文件必须被复制到其他应用程序中来让他们访问你service的接口，你必须维护原始接口的支持（向后兼容）。

#用一个实例来分步骤说明

##在server项目中建立.aidl文件　

![这里写图片描述](http://img.blog.csdn.net/20160504180944041)

　　AIDL使用一个简单的语法让你声明一个带有一个或者多个带有参数和返回值方法的接口 参数和返回值可以是任何类型，甚至是AIDL生成的接口。
　　
　　IService.aidl
　　

```

package com.example.aidl;


interface IService {
    
    String hello(String name); 
}
```

##在server项目中建立服务类

　　当你编译你的应用时，Android SDK工具生成一个.java接口文件用你的.aidl文件命名生成的接口包含一个名字为Stub的子类，这是一个它父类的抽象实现，并且声明了.aidl中所有的方法。
　　
　　Stub也定义了一些辅助的方法，最显著的就是asInterface()，它是用来接收一个IBinder（通常IBinder传递给客户端的onServiceConnected()回调方法）并且返回一个Stub接口的实例 。

　　一旦你为service实现了接口，你需要把它暴露给客户端，这样他们才能绑定到上面 为了给你的service暴露接口，继承Service并且实现onBind()方法返回一个你实现生成的Stub类。

　　　AIDLService.java

```
public class AIDLService extends Service {
	

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Return the interface
        return new IService.Stub() {
			@Override
			public String hello(String name) throws RemoteException {
				// TODO Auto-generated method stub
				return "hello"+name;
			}
		};
    }
```

##在server项目AndroidManifest中申明Service 

```
<service 
  android:name="com.example.service.AIDLService" >
　　 <intent-filter>  
<action android:name="android.intent.action.AIDLService" />  
　　</intent-filter> 
        </service>
```

##把server项目中的aidl文件带包拷贝到client项目中（包名要相同）

![这里写图片描述](http://img.blog.csdn.net/20160504181850678) 

MainActivity.java

```
public class MainActivity extends Activity {

	IService RemoteService; //监听服务
	private ServiceConnection mConnection = new ServiceConnection() {

		@Override
		public void onServiceConnected(ComponentName name, IBinder service) {
			// TODO Auto-generated method stub
			Log.i("mConnection", service+"");
			RemoteService = IService.Stub.asInterface(service);
			
			try {
				String s= RemoteService.hello("finch");
Toast.makeText(MainActivity.this, s, Toast.LENGTH_LONG).show();
			} catch (RemoteException e) {
				e.printStackTrace();
			}
		}

		@Override
		public void onServiceDisconnected(ComponentName name) {
			// TODO Auto-generated method stub
			
		}
	    
	};
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		initService();
	}
	//连接服务
		private void initService() {
			Intent i = new Intent( );
			i.setAction("android.intent.action.AIDLService");
			boolean ret = bindService(i, mConnection, Context.BIND_AUTO_CREATE);
		}
		
		//断开服务
		private void releaseService() {
			unbindService(mConnection);
			mConnection = null;
		}
		
	@Override
	protected void onDestroy() {
		super.onDestroy();
		releaseService();
	}
}
```

运行结果：

<img src="http://img.blog.csdn.net/20160504182305682" width="286" height="473" />


[文章中AIDL例子代码下载](http://download.csdn.net/detail/amazing7/9510133)