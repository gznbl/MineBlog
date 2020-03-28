###简述

说起Activity，每个Android开发人员应该都很熟悉，它是整个Android application的一个重要部分。四大组件之一，用于展示界面和用户交互，本文主要总结一下Activity跳转的常用方法。

####1.Intent

#####a.显式跳转

	
	/**1. 不带结果回调*/
  	Intent intent=new Intent(MainActivity.this, SecondActivity.class); 
	startActivity(intent);

	/**2. 带结果回调*/
	Intent intent=new Intent(MainActivity.this, SecondActivity.class); 
	startActivityForResult(intent, requestCode);

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		//todo 处理跳转页面返回的结果
	}


#####b.隐式跳转

在AndroidManifest.xml文件里配置所跳转到Activity的属性

	<activity
		android:name="com.example.android.tst.SecondActivity" 
		android:label="@string/title_activity_main" >
		<intent-filter>
			<action android:name="com.example.android.tst.SecondActivity"/>	
			<category android:name="android.intent.category.DEFAULT" />
		</intent-filter>
	</activity>

在需要跳转的地方写

	Intent intent=new Intent("com.example.android.tst.SecondActivity"); 
	startActivity(intent);

####2.外部H5页面启动App

清单文件对应Activity配置, 其中host, scheme, path自定义即可

	<intent-filter>
          <!--该页面的路径配置-->
          <data
               android:host="com.admin.example"
               android:path="/parameter"
               android:scheme="customscheme" />
          <!--下面这几行也必须得设置-->
          <category android:name="android.intent.category.DEFAULT" />
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.BROWSABLE" />
    </intent-filter>

h5页面这样配置

	var the_href = "customscheme://com.admin.example/parameter?param1=......"

####3.内部h5跳转activity

定义一个类用于js调用

	public class TestInterFace{
        Intent intent = null;
        private Context mContext;
        public TestInterFace(Context context){
            this.mContext = context;
        }
 
        @JavascriptInterface  //这个必须加，否则不能调用
        public  void jump(){
            intent = new Intent(mContext, TestActivity.class);
            startActivity(intent);
        }
    }

WebView配置

	mWebview.getSettings().setJavaScriptEnabled(true); 
    mWebview.loadData("", "text/html", null);
    mWebview.addJavascriptInterface(new TestInterFace(WebActivity.this),"test");
    mWebview.loadUrl("http://www.baidu.com/");
    //设置不用系统浏览器打开,直接显示在当前Webview
    mWebview.setWebViewClient(new WebViewClient() {
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, String url) {
              view.loadUrl(url);
              return true;
        }
    });

js调用
	
	<script>
		
        ```
		
		function jumpToAppPages(){
        		test.jump();
    	}

		```
	</script>




####4.反射启动activity

	/**className 为对应的activity的全路径*/
	public static void startActivity(String className){

        Intent intent = new Intent();
        try {
            Class aClass = Class.forName(className);
            intent.setClass(MyApplication.getInstance(),aClass);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            MyApplication.getInstance().startActivity(intent);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

