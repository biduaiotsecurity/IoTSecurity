# 百度IoTSecurity SDK接入文档

 [TOC] 

## 前置条件
1、请先注册 登录百度云

2、进入物接入IoT Core: https://console.bce.baidu.com/iot2/core/landing

3、创建项目

4、设置策略、身份、用户等信息

IoT Core文档: https://cloud.baidu.com/doc/IoTCore/s/pk7ophcd8

## 集成
```
repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
    compile(name: 'IoTSecurity-release-vxxx', ext: 'aar')

    // 如果项目中已集成过gson，可以忽略
    implementation  'com.squareup.okhttp3:okhttp:3.12.0'
    implementation 'com.google.code.gson:gson:2.8.2'
    implementation 'androidx.legacy:legacy-support-v4:1.0.0'
    implementation 'org.greenrobot:eventbus:3.0.0'
}
```

## 混淆配置
SDK已经做好混淆规则。


## 使用

### 初始化与配置
这里面的回调是Application的。
```
@Override
    public void onCreate() {
        super.onCreate();
        
		// 必须在super之后
		// 参数1 ： ctx
		// 参数2 ： 回调服务所在的包名,就是最终app的包名。
		// 参数3 ： 回调服务名
        IoTSecurity.onApplicationCreate(getApplicationContext(), getApplicationContext().getPackageName(),
                MyResultService.class.getName());
    }

    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
	IoTSecurity.attachBaseContext(this);
    }
```



### 调用方法。
这些方法都是静态的，你可以直接访问它们。如果接口返回失败，多数情况下是还没有加载好模块，稍等一会再尝试调用。
```
public class IoTSecurity {

     /**
     * 初始化接口
     * @param broker 服务器地址，可以在物接入项目中可以看到，推荐使用ssl方式
     * @param iotCoreId iotCore id，请与控制台内iotCore id保持一致
     * @param clientId 设备id，可以通过generateClientId获取，或者自定义，建议与deviceKey保持一致
     * @param deviceKey 连接deviceKey，物接入控制台创建用户后可以看到
     * @param deviceSecret 连接deviceSecret，物接入控制台创建用户后可以看到
     * @param publishTopic 发布的topic
     * @param subscribeTopic 订阅的topic
     * @param encryptType 密钥加密方式，这里可供选择的有：IoTSecurity.MD5、IoTSecurity.SHA256，推荐使用IoTSecurity.SHA256
     * @throws Exception 初始化异常时，会抛出异常
     */
     init(String broker, String iotCoreId, String clientId, String deviceKey, String deviceSecret, 
            String publishTopic,String subscribeTopic, @EncryptType String encryptType) throws Exception

     /**
     * 查看是SDK是否准备就绪
     *
     * @return true 就绪
     */
     public static boolean isInitialized()

     /**
     * 获取设备id
     * @return 设备id
     */
     public static String generateClientId()

     /**
     * 设置连接保持时间
     * @param interval 保持时间
     */
     public static void setKeepaliveInterval(int interval)

     /**
     * 设置是否自动重连
     * @param flag 是否重连
     */
     public static void setAutoReconnect(boolean flag)

     /**
     * 设置重连时间间隔
     * @param interval 时间间隔
     */
     public static void setReconnectBaseInterval(int interval)

     /**
     * 设置离线后，缓存数据长度
     * @param len 数据长度
     */
     public static void setOfflineBufferLen(int len)

     /**
     * 是否清除会话
     * @param flag 是否清除
     */
     public static void setCleanSession(boolean flag) 

     /**
     * 设置LastWill消息
     * @param topic LastWill消息topic
     * @param msg LastWill消息内容
     * @param isRetain 设置是否保留
     */
     public static void setLastWill(String topic, String msg, boolean isRetain)

     /**
     * 设置消息接受者
     * @param messageProcessor 消息接受者
     */
     public static void setMessageProcessor(RawMessageProcessor messageProcessor)

     /**
     * 设置连接事件接受者
     * @param notifier 连接事件接受者
     */
     public static void setMqttClientEventNotifier(MqttClientEventNotifier notifier) throws Exception

     /**
     * mqtt连接
     */
     public static Future<Void> connect()

     /**
     * 当前连接状态
     * @return 连接状态
     */
     public static boolean isConnected()

     /**
     * 发布消息
     * @param rawMessage 发布的消息
     */
     public static Future<Void> publishMessage(final byte[] rawMessage)

     /**
     * 断开连接
     */
     public static void close()
}
    
```


### 回调
需要自己建立一个service，继承自`DefaultResultService `。
```
public class MyResultService extends DefaultResultService {

    //所有功能是否已经准备好。
    @Override
    public void onInitialized(boolean b) {
    	// 在这里之前，可以一直转Loading...
        Log.i("onInitialized", "onInitialized");
    }
}
```

## 实验结果
### onInitialized
根据传递来的bool值，判断是否初始化完成，在这之前，界面可以先转个loading之类的。
