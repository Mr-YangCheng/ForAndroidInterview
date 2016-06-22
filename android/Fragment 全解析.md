#1．概述

　　Fragment是Activity中用户界面的一个行为或者是一部分。主要是支持在大屏幕上动态和更为灵活的去组合或是交换UI组件，通过将activity的布局分割成若干个fragment，可以在运行时编辑activity的呈现，并且那些变化会被保存在由activity管理的后台栈里面。

　　**Fragment必须总是被嵌入到一个activity之中**，并且fragment的生命周期直接受其宿主activity的生命周期的影响。你可以认为fragment是activity的一个模块零件，它有自己的生命周期，接收它自己的输入事件，并且可以在activity运行时添加或者删除。

　　应该将每一个fragment设计为模块化的和可复用化的activity组件。也就是说，你可以在多个activity中引用同一个fragment，因为fragment定义了它自己的布局，并且使用它本身生命周期回调的行为。


#２．Fragment的生命周期

先看fragment生命周期图：

![这里写图片描述](http://img.blog.csdn.net/20160429134558410) 

　　fragment所生存的activity生命周期直接影响着fragment的生命周期，由此针对activity的每一个生命周期回调都会引发一个fragment类似的回调。例如，当activity接收到onPause()时，这个activity之中的每个fragment都会接收到onPause()。
　　[这有Activity的详细说明](http://blog.csdn.net/amazing7/article/details/51244219)

　　Fragment有一些额外的生命周期回调方法（创建和销毁fragment界面）．

 - onAttach()

　　当fragment被绑定到activity时调用（Activity会被传入）。

 - onCreateView()

　　将本身的布局构建到activity中去（fragment作为activity界面的一部分）
　　

 -  onActivityCreated()

　　当activity的onCreate()函数返回时被调用。

 - onDestroyView()

　　当与fragment关联的视图体系正被移除时被调用。

 - onDetach()

　　当fragment正与activity解除关联时被调用。

当activity接收到它的onCreate()回调时，activity之中的fragment接收到onActivityCreated()回调。

　　一旦activity处于resumed状态，则可以在activity中自由的添加或者移除fragment。因此，只**有当activity处于resumed状态时**，fragment的生命周期才可以独立变化。
　　
fragment会在　activity离开恢复状态时　再一次被activity推入它的生命周期中。

**管理fragment生命周期**与管理activity生命周期很相像。像activity一样，fragment也有三种状态：

 - Resumed

　　fragment在运行中的activity可见。

 - Paused

　　另一个activity处于前台且得到焦点，但是这个fragment所在的activity仍然可见（前台activity部分透明，或者没有覆盖全屏）。

 - Stopped

　　fragment不可见。要么宿主activity已经停止，要么fragment已经从activity上移除，但已被添加到后台栈中。一个停止的fragment仍然活着（所有状态和成员信息仍然由系统保留着）。但是，它对用户来讲已经不再可见，并且如果activity被杀掉，它也将被杀掉。

　　如果activity的进程被杀掉了，在activity被重新创建时，你需要恢复fragment状态。可以执行fragment的onSaveInstanceState()来保存状态（注意在fragment是在onCreate()，onCreateView()，或onActvityCreate()中进行恢复）。

　　在生命周期方面,activity与fragment之间一个**很重要的不同**，就是在各自的后台栈中是如何存储的。
　　当activity停止时，**默认**情况下activity被安置在由系统管理的activity后台栈中；　
　　fragment仅当在一个事务被移除时，通过显式调用addToBackStack()请求保存的实例，该fragment才被置于由宿主activity管理的后台栈。　

**要创建一个fragment**，必须创建一个fragment的子类。一般情况下，我们至少需要实现以下几个fragment生命周期方法：

> onCreate()

　　在创建fragment时系统会调用此方法。在实现代码中，你可以初始化想要在fragment中保持的那些必要组件，当fragment处于暂停或者停止状态之后可重新启用它们。

>onCreateView()

　　在第一次为fragment绘制用户界面时系统会调用此方法。为fragment绘制用户界面，这个函数必须要返回所绘出的fragment的根View。如果fragment没有用户界面可以返回空。

　　
```
@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,Bundle savedInstanceState) {　
		
			// Inflate the layout for this fragment
return inflater.inflate(R.layout.example_fragment, container, false);
		}
```
inflate()函数需要以下三个参数：

①要inflate的布局的资源ID。　

②被inflate的布局的父ViewGroup。

③一个布尔值，表明在inflate期间被infalte的布局是否应该附上ViewGroup（第二个参数container）。（在这个例子中传入的是false，因为系统已经将被inflate的布局插入到容器中（container）——传入true会在最终的布局里创建一个多余的ViewGroup。）　

> onPause()

　　系统回调用该函数作为用户离开fragment的第一个预兆（尽管这并不总意味着fragment被销毁）。在当前用户会话结束之前，通常要在这里提交任何应该持久化的变化（因为用户可能不再返回）。


#3.将fragment添加到activity之中

　　可以通过在activity布局文件中声明fragment，用fragment标签把fragment插入到activity的布局中，或者是用应用程序源码将它添加到一个存在的ViewGroup中。　
　　
　　但fragment并不是一个定要作为activity布局的一部分，fragment也可以为activity隐身工作。



##3.1在activity的布局文件里声明fragment

　　可以像为view一样为fragment指定布局属性。例如：

```
<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:orientation="horizontal"
		android:layout_width="match_parent"
		android:layout_height="match_parent">　
		
		<fragment android:name="com.example.test.FragmentOne"
				android:id="@+id/fo"
				android:layout_width="match_parent"
				android:layout_height="match_parent" />
	</LinearLayout>

```
　　fragment标签中的android:name 属性指定了布局中实例化的Fragment类。

　　当系统创建activity布局时，它实例化了布局文件中指定的每一个fragment，并为它们调用onCreateView()函数，以获取每一个fragment的布局。系统直接在<fragment>元素的位置插入fragment返回的View。

　　注意：每个fragment都需要一个唯一的标识，如果重启activity，系统可用来恢复fragment（并且可用来捕捉fragment的事务处理，例如移除）。为fragment提供ID有三种方法：

 - 

用android:id属性提供一个唯一的标识。　

 - 用android:tag属性提供一个唯一的字符串。　
 
 - 如果上述两个属性都没有，系统会使用其容器视图（view）的ID。　

##3.2通过编码将fragment添加到已存在的ViewGroup中

　　在activity运行的任何时候，你都可以将fragment添加到activity布局中。
　　
　　要管理activity中的fragment，可以使用FragmentManager。可以通过在activity中调用getFragmentManager()获得。使用FragmentManager 可以做如下事情，包括：

 - 使用findFragmentById()（用于在activity布局中提供有界面的fragment）或者findFragmentByTag()获取activity中存在的fragment（用于有界面或者没有界面的fragment）。　　
 
 - 使用popBackStack()（模仿用户的BACK命令）从后台栈弹出fragment。　　
 
 
 - 使用addOnBackStackChangedListener()注册一个监听后台栈变化的监听器。

在Android中，对Fragment的事务操作都是通过FragmentTransaction来执行。操作大致可以分为两类：

 - 显示：add() replace() show() attach()　　
 
 - 隐藏：remove() hide() detach()　

> 说明：
　　调用show() & hide()方法时，Fragment的生命周期方法并不会被执行，仅仅是Fragment的View被显示或者​隐藏。

>　　执行replace()时（至少两个Fragment），会执行第二个Fragment的onAttach()方法、执行第一个Fragment的onPause()-onDetach()方法，同时containerView会detach第一个Fragment的View。

>　　add()方法执行onAttach()-onResume()的生命周期，相对的remove()就是执行完成剩下的onPause()-onDetach()周期。


可以像下面这样从Activity中取得FragmentTransaction的实例：

```
FragmentManager fragmentManager = getFragmentManager()　
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
```

可以用add()函数添加fragment，并指定要添加的fragment以及要将其插入到哪个视图（view）之中（注意commit事务）：

```
ExampleFragment fragment = new ExampleFragment();
	fragmentTransaction.add(R.id.fragment_container, fragment);
	fragmentTransaction.commit();
```

##3.3添加没有界面的fragment 

　　也可以使用fragment为activity提供后台动作，却不呈现多余的用户界面。

　　想要添加没有界面的fragment ，可以使用add(Fragment, String)（为fragment提供一个唯一的字符串“tag”，而不是视图（view）ID）。这样添加了fragment，但是，因为还没有关联到activity布局中的视图（view） ，收不到onCreateView()的调用。所以不需要实现这个方法。　
　　
　　对于无界面fragment，字符串标签是**唯一识别**它的方法。如果之后想从activity中取到fragment，需要使用findFragmentByTag()。　


#4.fragment事务后台栈

　　在调用commit()之前，可以将事务添加到fragment事务后台栈中（通过调用addToBackStatck()）。这个后台栈由activity管理，并且允许用户通过按BACK键回退到前一个fragment状态。

　　下面的代码中一个fragment代替另一个fragment，并且将之前的fragment状态保留在后台栈中：

```
 Fragment newFragment = new ExampleFragment();
 FragmentTransaction transaction = getFragmentManager().beginTransaction();
 
 transaction.replace(R.id.fragment_container, newFragment);
 transaction.addToBackStack(null);

 transaction.commit();
```

> 注意：
> 
>　　 如果添加多个变更事务（例如另一个add()或者remove()）并调用addToBackStack()，那么在调用commit()之前的所有应用的变更被作为一个单独的事务添加到后台栈中，并且BACK键可以将它们一起回退。
> 
> 　　当移除一个fragment时，如果调用了addToBackStack()，那么之后fragment会被停止，如果用户回退，它将被恢复过来。
> 
>　　调用commit()并不立刻执行事务，相反，而是采取预约方式，一旦activity的界面线程（主线程）准备好便可运行起来。然而，如果有必要的话，你可以从界面线程调用executePendingTransations()立即执行由commit()提交的事务。
> 
> 　　只能在activity保存状态（当用户离开activity时）之前用commit()提交事务。如果你尝试在那时之后提交，会抛出一个异常。这是因为如果activity需要被恢复，提交后的状态会被丢失。对于这类丢失提交的情况，可使用commitAllowingStateLoss()


#５.与Activity交互

　　

 - Activity中已经有了该Fragment的引用，直接通过该引用进行交互。
 
 -如果没引用可以通过调用fragment的函数findFragmentById()或者findFragmentByTag()，从FragmentManager中获取Fragment的索引，例如： 

　

```
ExampleFragment fragment = (ExampleFragment) getFragmentManager().findFragmentById(R.id.example_fragment);
```

 - 在Fragment中可以通过getActivity得到当前绑定的Activity的实例。
 
 
 
 - 创建activity事件回调函数，在fragment内部定义一个回调接口，宿主activity来实现它。



 

	