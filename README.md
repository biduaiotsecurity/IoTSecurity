# 百度IoTSecurity SDK接入文档

## 介绍
百度天工是融合了百度ABC（AI、Big Data、Cloud）的“一站式、全托管”智能物联网平台，其中物联网核心套件IoT Core为广大厂商提供设备接入、通信、管理服务，实现设备上云。在此服务基础上，IoTSecurity SDK提供“云-管-端”一体的安全防护方案，实现设备与云端之间的安全可靠双向连接，保障设备上云安全。

## 接入前置条件
1、注册并登录百度云

2、进入百度天工IoT Core: https://console.bce.baidu.com/iot2/core/landing

3、创建IoT Core，获取CoreID

4、创建设备模板，使用模板创建设备并获取DeviceKey、DeviceSecret，IoT Core文档: https://cloud.baidu.com/doc/IoTCore/s/pk7ophcd8

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
    implementation 'org.eclipse.paho:org.eclipse.paho.client.mqttv3:1.2.0'
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
		// 参数3 ： 回调服务名,需要自己创建service
        IoTSecurity.onApplicationCreate(getApplicationContext(), getApplicationContext().getPackageName(),
                MyResultService.class.getName());
    }

    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
	    IoTSecurity.attachBaseContext(this);
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


### 调用方法。
这些方法都是静态的，你可以直接访问它们。如果接口返回失败，多数情况下是还没有加载好模块，稍等一会再尝试调用。
```
public class IoTSecurity {

     /**
     * 查看是SDK是否准备就绪
     *
     * @return true 就绪
     */
     public static boolean isInitialized()

     /**
     * 初始化方法
     * @param broker 服务器地址，可以在物接入项目中可以看到，推荐使用ssl方式
     * @param iotCoreId iotCore id，请与控制台内iotCore id保持一致
     * @param clientId 设备id，可以通过generateClientId获取，或者自定义，建议与deviceKey保持一致
     * @param deviceKey 连接deviceKey，物接入控制台创建用户后可以看到
     * @param deviceSecret 连接deviceSecret，物接入控制台创建用户后可以看到
     * @param encryptType 密钥加密方式，这里可供选择的有："MD5"、"SHA256"，推荐使用"SHA256"
     * @throws MqttException 初始化异常时，会抛出异常
     */
     public static void init(String broker, String iotCoreId,
                            String clientId, String deviceKey,
                            String deviceSecret, String encryptType) throws MqttException

     /**
     * 同MqttClient.generateClientId方法，获取设备id，建议直接用deviceKey作为clientid
     * @return 设备id
     */
     public static String generateClientId()

     /**
     * 同MqttAsyncClient的connect方法
     * options中不需要填充username跟password，内部会根据init接口的参数计算出username跟password
     */
     public static IMqttToken connect(MqttConnectOptions options, Object userContext,
                                     IMqttActionListener callback) throws MqttException


     /**
     * 同MqttAsyncClient的subscribe方法
     */
    public static IMqttToken subscribe(String topicFilter, int qos, Object userContext,
                                       IMqttActionListener callback) throws MqttException


     /**
     * 同MqttAsyncClient的disconnect方法
     */
    public static IMqttToken disconnect() throws MqttException 

     /**
     * 同MqttAsyncClient的isConnected
     */
    public static boolean isConnected()

     /**
     * 同MqttAsyncClient的getClientId
     */
    public static String getClientId()

     /**
     * 同MqttAsyncClient的getServerURI
     */
    public static String getServerURI()
     
     /**
     * 同MqttAsyncClient的getCurrentServerURI
     */
    public static String getCurrentServerURI()
     
     /**
     * 同MqttAsyncClient的unsubscribe
     */
    public static IMqttToken unsubscribe(String topicFilter, Object userContext, IMqttActionListener callback) throws MqttException
     
     /**
     * 同MqttAsyncClient的setCallback
     */
    public static void setCallback(MqttCallback callback)
     
     /**
     * 同MqttAsyncClient的publish
     */
    public static IMqttDeliveryToken publish(String topic, byte[] payload, int qos, boolean retained, Object userContext, IMqttActionListener callback)throws MqttException
     
     /**
     * 同MqttAsyncClient的reconnect
     */
    public static void reconnect() throws MqttException
     
     /**
     * 同MqttAsyncClient的setBufferOpts
     */
    public static void setBufferOpts(DisconnectedBufferOptions bufferOpts)
     
     /**
     * 同MqttAsyncClient的getBufferedMessageCount
     */
    public static int getBufferedMessageCount()
     
     /**
     * 同MqttAsyncClient的getBufferedMessage
     */
    public static MqttMessage getBufferedMessage(int bufferIndex)
     
     /**
     * 同MqttAsyncClient的deleteBufferedMessage
     */
    public static void deleteBufferedMessage(int bufferIndex)
     
     /**
     * 同MqttAsyncClient的getInFlightMessageCount
     */
    public static int getInFlightMessageCount()
     
     /**
     * 同MqttAsyncClient的close
     */
    public static void close(boolean force) throws MqttException
}
    
```



