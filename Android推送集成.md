###Android推送

	推送技术哪家强，中国山东找......
	不对，找统一推送联盟（不过这是后话，目前革命尚未成功，仍需努力）

####1.工程配置

Android机型千千万，推送方式也是各有千秋。本文主要介绍华为，小米和友盟三种常用推送方式。因为官方文档已经介绍很详细了，所以这里仅给出文档入口。

> * [**华为推送官方文档**](https://developer.huawei.com/consumer/cn/service/hms/catalog/huaweipush_v3.html?page=hmssdk_huaweipush_devprepare_v3)


> * [**小米推送官方文档**](https://dev.mi.com/console/doc/detail?pId=100)


> * [**友盟推送官方文档**](https://developer.umeng.com/docs/66632/detail/98581)

注意事项
	
> 1.工程配置时可参照官方最新demeo，注意各版本号对应,版本号不一致有可能导致编译不通过

>2.友盟推送可以实现常用机型的正常推送，小米推送和华为推送主要是为了在对应机型上app没有运行时也能接收到推送

####2.App接收到通知后的页面跳转



    /**
     * 点击通知后的动作
     * @param className 页面全路径
     * @param mExtra 参数
     */
    public static void dealWithNotification(String className, Map<String,String> mExtra){
        Intent intent = new Intent();
        Bundle bundle = new Bundle();
        if(mExtra!=null){
            Set<Map.Entry<String, String>> entries = mExtra.entrySet();
            for(Iterator iterator = entries.iterator(); iterator.hasNext();){
                Map.Entry entry = (Map.Entry) iterator.next();
                String key = (String)entry.getKey();
                String value = (String)entry.getValue();
                bundle.putString(key,value);
            }
            intent.putExtras(bundle);
        }
        try {
            Class aClass = Class.forName(className);
            intent.setClass(Appconfig.getContext(),aClass);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            PLApplicationIml.getInstance().startActivity(intent);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

