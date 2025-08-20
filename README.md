# zawsdk_android
[![Latest release](https://img.shields.io/github/v/release/bigbigbig/zawsdk_android)](https://github.com/bigbigbig/zawsdk_android/releases/latest)

## Install sdk
#### google配置
在 `app` module 下 引入 `google-services.json` 文件<br><br>
在 根目录 `build.gradle` 文件中声明 plugin
```groovy
plugins {
    id 'com.google.gms.google-services' version '4.3.15' apply false
}
```
在 `app` module 下的 `build.gradle` 文件中使用 plugin
```groovy
plugins {
    id 'com.google.gms.google-services'
}
```
#### Facebook配置
在 `app` module `AndroidManifest.xml` 文件中添加 Facebook 参数
```xml
<meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>
<meta-data android:name="com.facebook.sdk.ClientToken" android:value="@string/facebook_client_token"/>
```
在 `application` 元素中，为 Facebook 添加活动，并为 Chrome 自定义选项卡添加活动和意向筛选条件
```xml
<activity android:name="com.facebook.FacebookActivity"
android:configChanges=
	"keyboard|keyboardHidden|screenLayout|screenSize|orientation"
android:label="@string/app_name" />
<activity
android:name="com.facebook.CustomTabActivity"
android:exported="true">
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="@string/fb_login_protocol_scheme" />
</intent-filter>
</activity>
```
将 ContentProvider 添加至 AndroidManifest.xml 文件，并将 {APP_ID} 设置为您的应用编号
```xml
<provider android:authorities="com.facebook.app.FacebookContentProvider{APP_ID}"
android:name="com.facebook.FacebookContentProvider"
android:exported="true"/>
```
如果您的应用程序面向 Android 11 或更高版本，请向 AndroidManifest.xml 文件添加以下查询块，以使 Facebook 应用对您的应用可见：
```xml
<queries><provider android:authorities="com.facebook.katana.provider.PlatformProvider" /></queries>
```
#### VK配置(可选)
如果项目中需要接入 vk 登录，则进行如下操作
在 根目录`setting.gradle`文件中添加vk maven 源
```groovy
luginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
        maven{url "https://artifactory-external.vkpartner.ru/artifactory/vkid-sdk-android/"}
        maven{url "https://artifactory-external.vkpartner.ru/artifactory/maven/"}
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            url "https://artifactory-external.vkpartner.ru/artifactory/vkid-sdk-android/"
        }
        maven {
            url "https://artifactory-external.vkpartner.ru/artifactory/maven/"
        }
        maven {
            url "https://artifactory-external.vkpartner.ru/artifactory/vk-id-captcha/android/"
        }
    }
}
```
添加 vk id 参数
在 app/build.gradle 中添加如下vk 配置
```groovy
    defaultConfig {
        ...

        manifestPlaceholders = [
                "VKIDClientID" : "{vk_app_id}",
                "VKIDRedirectHost" : "vk.com",
                "VKIDClientSecret" : "{vk_secure_secret}",
                "VKIDRedirectScheme" : "vk{vk_app_id}"
        ]
    }
```
demo
```groovy
manifestPlaceholders = [
                "VKIDClientID" : "111111",
                "VKIDRedirectHost" : "vk.com",
                "VKIDClientSecret" : "UHdfefdee783e7e",
                "VKIDRedirectScheme" : "vk111111"
        ]
```
在 app/build.gradle 中添加如下依赖, 版本号和主 sdk 版本号保持一致
```
    implementation 'com.zawsdk:zawsdk_vk_android:0.6.3'
```

#### sdk安装 
在 根目录`setting.gradle` 文件中添加 maven 源
```groovy
pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
        maven {
            url "https://raw.githubusercontent.com/bigbigbig/zawsdk_android/main"
//            url "https://gitee.com/zhangmingsheng_1992/zawsdk_android2/tree/main"
        }
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            url "https://raw.githubusercontent.com/bigbigbig/zawsdk_android/main"
//            url "https://gitee.com/zhangmingsheng_1992/zawsdk_android2/tree/main"
        }
    }
}
```
在 `app` module 下的 `build.gradle` 中添加sdk依赖
```groovy
//latest_release 替换为Readme顶部的版本号，不用带字母v
dependencies {
    implementation 'com.zawsdk:zawsdk_android:latest_release'
}

```
#### 混淆规则
```
-keep class com.adjust.sdk.** { *; }
-keep class com.google.android.gms.common.ConnectionResult {
   int SUCCESS;
}
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient {
   com.google.android.gms.ads.identifier.AdvertisingIdClient$Info getAdvertisingIdInfo(android.content.Context);
}
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient$Info {
   java.lang.String getId();
   boolean isLimitAdTrackingEnabled();
}
-keep public class com.android.installreferrer.** { *; }
```
## 关于登录的说明
#### 登录方式的枚举值
```java
    public static int FACEBOOK = 1;
    public static int GOOGLE = 2;
    public static int APPLE = 3;
    public static int GUEST = 4;
```
#### 我们推荐的登录方式
>直接调用 `quickLogin`，`quickLogin`方式 首次进入 app 为静默游客登录，之后进入 app 为快速登录上次的登录方式，如果用户通过退出登录`logout`清空了本地缓存的登录信息，则下次调用`quickLogin`会拉起登录界面

#### 如何切换账号
>调用 `login` 方法，即可切换账号，当前是登录状态，依然可以通过该方法切换账号
#### 游客登录绑定三方账号
>登录相关的接口返回的 loginModel 模型, 含有 loginType 和 bindType 两个字段，只有loginType 为 4 游客，且 bindType 为 0 未绑定，才需要显示绑定入口，绑定时调用 `bind` 方法即可
#### 不想用`quickLogin`的首次静默游客登录该如何处理?
```java
boolean hasLogin = this.getSharedPreferences("SP", 0).getBoolean("hasLogin", false);
if (hasLogin){
	ZAWSDK.getInstance().quickLogin(MainActivity.this, new ResultCallback<LoginModel>() {
		@Override
		public void onSuccess(LoginModel var1) {
			Log.v("SDK", "登录成功");
		}
	
		@Override
		public void onFailure(int code, String var1) {
			Log.i("SDK", "登录失败");
		}
	});
}else {
	ZAWSDK.getInstance().login(MainActivity.this, new ResultCallback<LoginModel>() {
		@Override
		public void onSuccess(LoginModel var1) {
			Log.v("SDK", "登录成功");
			Log.v("SDK", "token" + var1.getToken());
			MainActivity.this.getSharedPreferences("SP", 0).edit().putBoolean("hasLogin", true).apply();
			Toast.makeText(MainActivity.this, "登录成功", Toast.LENGTH_SHORT).show();
		}

		@Override
		public void onFailure(int code, String var1) {

		}
	});
}
```
# sdk 使用
#### 主要数据结构说明
###### 登录类型
```java
    public static int FACEBOOK = 1;
    public static int GOOGLE = 2;
    public static int GUEST = 4;
```
###### 游客登录绑定条件
```
只有bindType是0且loginType是4的情况需要绑定
```
###### 登录绑定回调
```java
public class LoginModel {
    private String uid;
    private String token;
    private int loginType;//账户首次登录类型
    private int bindType;//账户绑定类型

    LoginModel(String uid, String token, int loginType, int bindType) {
        this.uid = uid;
        this.token = token;
        this.loginType = loginType;
        this.bindType = bindType;
    }

    public String getUid() {
        return uid;
    }

    public String getToken() {
        return token;
    }

    public int getLoginType() {
        return loginType;
    }

    public int getBindType() {
        return bindType;
    }
}
```
###### 支付回调
```java
public class PayModel {
    private final String orderId;//订单 id
    private final String cpOrderId;// cp 订单 id
    private final String thirdOrderId;// 三方订单 id/不一定有
    private final String purchaseToken;// 三方购买凭证
    private final int payMethod;// 支付方式/预留字段/默认 0
    PayModel(String orderId, String cpOrderId, String thirdOrderId, String purchaseToken, int payMethod){
        this.orderId = orderId;
        this.cpOrderId = cpOrderId;
        this.thirdOrderId = thirdOrderId;
        this.purchaseToken = purchaseToken;
        this.payMethod = payMethod;
    }

    public String getPurchaseToken() {
        return purchaseToken;
    }

    public String getOrderId() {
        return orderId;
    }

    public String getCpOrderId() {
        return cpOrderId;
    }

    public String getThirdOrderId() {
        return thirdOrderId;
    }

    public int getPayMethod() {
        return payMethod;
    }
}
```
#### 其他
#### 支付和界面回调的基础
一定别忘了加上，否则支付和界面登录在前端无法收到回调
```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // TODO Auto-generated method stub
        super.onActivityResult(requestCode, resultCode, data);
        ZAWSDK.getInstance().onActivityResult(requestCode, resultCode,
                data);
    }
```
#### 初始化
在自定义的 application 的 onCreate 方法中调用 sdk 的 onCreate 方法
```java
ZAWSDK.getInstance().onCreate(this, 渠道号, 项目 id, 授权 key, 你的隐私协议, mmp_key, Google登录的webClientId);
```
#### 如何自定义application
创建类`GlobalApplication`继承`Application`, 实现它的`onCreate`方法
```java
public class GlobalApplication extends Application {

    private String appid = "x";
    private String appkey = "xxxxxxxxxxxxx";
    private String channel = "xxxxx";
    private String mmp_key = "xxxxxxxxxxx";
    private String policy_url = "https://xxxx.com";
    private String google_web_client_id = "xxxxxxxxx-xxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com";
    @Override
    public void onCreate() {
        super.onCreate();
        ZAWSDK.getInstance().onCreate(this, channel, appid, appkey, policy_url, mmp_key, google_web_client_id);
    }
}
```
在 `app/src/main/AndroidManifest.xml` 文件中 找到 `<application` 标签
在标签下添加 `android:name`属性，设置属性值为`xxx.xxx.xxx.xxx.GlobalApplication`,其中`xxx.xxx.xxx.xxx`为类`GlobalApplication`所属的包名
```xml
 <application
        android:name="xxx.xxx.xxx.xxx.GlobalApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
```



#### 快速登录
```java
//该方法为静默登录，首次为游客登录，之后会使用上次的登录账户
//如果用户选择了退出登录或者静默登录失败，该方法则会拉起界面登录
ZAWSDK.getInstance().quickLogin(MainActivity.this, new ResultCallback<LoginModel>() {
				@Override
				public void onSuccess(LoginModel var1) {
					Log.v("SDK", "登录成功");
				}

				@Override
				public void onFailure(int code, String var1) {
					Log.i("SDK", "登录失败");
				}
			});
```
#### 切换账号
```java
ZAWSDK.getInstance().account(MainActivity.this, new SwitchAccountCallback() {
				@Override
				public void didSwitchAccount() {
					Log.v("SDK", "切换账号点击");
				}
			}, new ResultCallback<LoginModel>() {
				@Override
				public void onSuccess(LoginModel var1) {
					Log.v("SDK", "account 登录成功");
					Log.v("SDK", "token" + var1.getToken());
				}

				@Override
				public void onFailure(int code, String var1) {

				}
			});
```
#### 界面登录
```java
//该方法会拉起登录界面
ZAWSDK.getInstance().login(MainActivity.this, new ResultCallback<LoginModel>() {
				@Override
				public void onSuccess(LoginModel var1) {
					Log.v("SDK", "登录成功");
					Log.v("SDK", "token" + var1.getToken());
					Toast.makeText(MainActivity.this, "登录成功", Toast.LENGTH_SHORT).show();
				}

				@Override
				public void onFailure(int code, String var1) {

				}
			});
```

#### 退出登录
```java
//该方法会清空本地缓存的登录信息
ZAWSDK.getInstance().logout(new ResultCallback<Void>() {
				@Override
				public void onSuccess(Void var1) {
					Log.v("SDK", "退出登录成功");
				}

				@Override
				public void onFailure(int code, String var1) {
					Log.v("SDK", "退出登录失败");
				}
			});
```

#### 游客登录绑定
```java
//如果用户使用的是游客登录，可使用这种方式将账号和三方登录绑定
ZAWSDK.getInstance().bind(MainActivity.this, new ResultCallback<LoginModel>() {
				@Override
				public void onSuccess(LoginModel var1) {
					Log.v("SDK", "绑定成功");
				}

				@Override
				public void onFailure(int code, String var1) {
					Log.v("SDK", "绑定失败");
				}
			});
```

#### 上传角色信息
```java
ZAWSDK.getInstance().updateRoleInfo(sdk的用户id, 服务器 id, 服务器名称, 角色 id, 角色名称, 角色等级,
					new ResultCallback<UserRoleInfo>() {

						@Override
						public void onSuccess(UserRoleInfo data) {

						}

						@Override
						public void onFailure(int code, String var1) {

						}

					});
```

#### google 支付
```java
ZAWSDK.getInstance().payGoogle(MainActivity.this, 金额, 服务器 id,  商品名称, google商品 id, 是否测试,
        接入方订单 id,  接入方可以在订单上附加的额外信息, new ResultCallback<PayModel>() {

            @Override
            public void onSuccess(PayModel var1) {
                Log.i("SDK", "onSuccess:");
            }

            @Override
            public void onFailure(int code, String var1) {
                Log.i("SDK", "onFailure:");
            }
        });
```

#### 接入方自定义事件上报
>付费事件不需要接入方上报，如果接入方有某些想上报的自定义事件，可以通过该方法上报
###### Adjust
> 版本0.3.9开始支持Adjust
```java
ZAWSDK.getInstance().adLogEvent("xxx");
//充值事件 cp 不需上报，此接口仅为预留
ZAWSDK.getInstance().adLogRevenueEvent("xxx", 1.0, "USD");
```
###### Appsflyer
> 版本0.3.9开始不再使用Appsflyer
```java
Map<String, Object> eventValues = new HashMap<String, Object>();
eventValues.put(AFInAppEventParameterName.PRICE, 1234.56);
eventValues.put(AFInAppEventParameterName.CONTENT_ID,"1234567");
ZAWSDK.getInstance().afLogEvent(this, "xxx", eventValues);
```

#### facebook 分享
```java
ZAWSDK.shareFacebook(this, null, "xxx");
```
