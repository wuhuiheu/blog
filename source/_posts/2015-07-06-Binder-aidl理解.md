title: Binder-aidl理解
date: 2015-07-06 11:50:59
tags: [Android,Binder,AIDL,代理模式]
---

***
详细可以看这几篇博文：

1. [http://blog.csdn.net/singwhatiwanna/article/details/44590179](http://blog.csdn.net/singwhatiwanna/article/details/44590179)
2. [http://blog.csdn.net/stonecao/article/details/6579333](http://blog.csdn.net/stonecao/article/details/6579333)
3. [http://blog.csdn.net/stonecao/article/details/6657438](http://blog.csdn.net/stonecao/article/details/6657438)
***

***
自己根据这几篇文章写了一些例子，加强印象。

1.定义接口
	package com.example.binderdemo;

	import android.os.IBinder;
	import android.os.IInterface;
	import android.os.RemoteException;

	public interface IBank extends IInterface {
    
	    static final String DESCRIPTOR = "com.example.binderdemo.IBank";
	    static final int TRANSACTION_queryMoney = (IBinder.FIRST_CALL_TRANSACTION + 0);
	
	
	
	    public long queryMoney(int uid) throws RemoteException;

	}

2.实现Binder

	package com.example.binderdemo;
	
	import android.os.Binder;
	import android.os.IBinder;
	import android.os.IInterface;
	import android.os.Parcel;
	import android.os.RemoteException;
	import android.util.Log;
	
	public class BankImpl extends Binder implements IBank {

	    public BankImpl() {
	        this.attachInterface(this, DESCRIPTOR);
	    }
	    
	    /** 如果是在同一个应用中绑定本应用，则直接返回BankImpl,否则直接返回Proxy*/
	    public static IBank asInterface(IBinder obj) {
	        //如果在同一应用中绑定，则obj为BankImpl实例，所以返回值为bankImpl
	        //否则为android.os.Binder$BinderProxy实例,所以返回Proxy
	        Log.d("ljhtest", "" + obj);
	        if (obj == null) {
	            return null;
	        }
	        IInterface iInterface = obj.queryLocalInterface(DESCRIPTOR);
	        if (iInterface != null && iInterface instanceof IBank) {
	            return (IBank) iInterface;
	        }
	
	        return new BankImpl.Proxy(obj);
	    }
	
	    @Override
	    public IBinder asBinder() {
	        return this;
	    }
	
	    @Override
	    protected boolean onTransact(int code, Parcel data, Parcel reply, int flags) throws RemoteException {
	        switch (code) {
	            case INTERFACE_TRANSACTION:
	                reply.writeString(DESCRIPTOR);
	                return true;
	            case TRANSACTION_queryMoney:
	                data.enforceInterface(DESCRIPTOR);
	                int uid = data.readInt();
	                long money = this.queryMoney(uid);
	                reply.writeNoException();
	                reply.writeLong(money);
	                return true;
	            default:
	                break;
	        }
	        return super.onTransact(code, data, reply, flags);
	    }
	
	    //同一应用中直接返回结果          
	    @Override
	    public long queryMoney(int uid) throws RemoteException {
	        return uid * 10;
	    }
	
	    private static class Proxy implements IBank {
	        private IBinder mRemote;
	
	        public Proxy(IBinder remote) {//这个remote为BinderProxy实例
	            this.mRemote = remote;
	        }
	
	        @Override
	        public IBinder asBinder() {
	            return mRemote;
	        }
	
	        @Override
	        public long queryMoney(int uid) throws RemoteException {
	            Parcel data = Parcel.obtain();
	            Parcel reply = Parcel.obtain();
	            long result;
	            try {
	                data.writeInterfaceToken(DESCRIPTOR);
	                data.writeInt(uid);
	                mRemote.transact(TRANSACTION_queryMoney, data, reply, 0);
	                reply.readException();
	                result = reply.readLong();
	            } finally {
	                reply.recycle();
	                data.recycle();
	            }
	
	            return result;
	        }
	
	    }

	}

3.定义Service


	package com.example.binderdemo;
	
	import android.app.Service;
	import android.content.Intent;
	import android.os.IBinder;
	import android.util.Log;
	
	public class BankQueryService extends Service {
	    private BankImpl mBankImpl = new BankImpl();
	
	    @Override
	    public void onCreate() {
	        Log.d("ljhtest", "onCreate");
	        super.onCreate();
	    }
	    
	    //绑定时返回接口
	    @Override
	    public IBinder onBind(Intent intent) {
	        Log.d("ljhtest", "onBind");
	        return mBankImpl;
	    }
	
	}

***
其实就像按照aidl一些，IBank就相当于aidl文件,BankImpl就相当于IDE为我们生成的文件一样





