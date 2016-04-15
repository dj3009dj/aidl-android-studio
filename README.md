# aidl-android-studio
aidl在android开发中是比较基础的东西，可以实现不同进程间的通信<br/>
使用android studio讲解一个简单例子，步骤如下：<br/>
1. android studio创建一个black activity<br/>
2. 右键src>New>AIDL>AIDL file，创建一个新的aidl文件，这里我命名为IAdditionService.aidl<br/>
3. 在这个aidl文件中定义一些接口，比如
<pre>
interface IAddtionService{
int add(in int x,in int y);
}
</pre>
4.Build>Make Project之后，在app\build\generated\source\debug\com.example.sijuding.aidl_demo中会生成IAddtionService.java<br/>
5.IAdditionService.java本质就是使用Proxy代理，比如在该java文件中实现了
<pre>
public static abstract class Stub extends android.os.IBinder implements IAddtionService{
...
public static IAddtionService asInterface(IBinder){...}
...
}
</pre>
6.写一个Service类，其中的onBind方法的目的是：实现Service与Activity交互。具体流程如下：<br/>
<pre>
Context调用bindService(Intent service, ServiceConnection conn,int flags)
--->调用Service的onCreate--->调用Service的onBind--->绑定成功调用ServiceConnection的onServiceConnected方法
</pre>
7.其中onBind的方法原型是<br/>
<pre>
public IBinder onBind(Intent intent)
{
 return new IAddtionService.Stub(){
 @Override
 public int add(int x,int y){return x+y;}
 }
}
</pre>
绑定Service成功后，onBind的返回值会传递到MainActivity中的ServiceConnection的onServiceConnected方法，这个方法的原型是<br/>
<pre>
public void onServiceConnected(..,IBinder service){
IAddtionService mService=IAddtionService.Stub.asInterface(service);
}
</pre>
8.这样mService就从后台传递到Activity中了，利用mService就可以在Activity中调用add方法了。
