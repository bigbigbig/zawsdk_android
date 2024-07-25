# zawsdk_android

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
#### facebook配置
在 `app` module `AndroidManifest.xml` 文件中添加 facebook 参数
```xml
<meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>
<meta-data android:name="com.facebook.sdk.ClientToken" android:value="@string/facebook_client_token"/>
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
        }
    }
}
```
在 `app` module 下的 `build.gradle` 中添加sdk依赖
```groovy
dependencies {
    implementation 'com.zawsdk:zawsdk_android:0.3.2'
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
#### 初始化
```java
ZAWSDK.getInstance().init(this, 渠道号, 项目 id, 授权 key, 你的隐私协议, afDevKey, Google登录的webClientId, new ResultCallback<Void>() {
			@Override
			public void onSuccess(Void var1) {
                		//初始化成功后调用 quicklogin 实现静默登录
				ZAWSDK.getInstance().quickLogin(MainActivity.this, new ResultCallback<LoginModel>() {
					@Override
					public void onSuccess(LoginModel var1) {
						Log.v("SDK", "登录成功");
						Log.v("SDK", "token" + var1.getToken());
					}

					@Override
					public void onFailure(int code, String var1) {
						Log.i("SDK", "登录失败");
					}
				});
			}

			@Override
			public void onFailure(int code, String var1) {
				Log.v("SDK", "初始化失败");
			}
		});
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

#### facebook 分享
```java
ZAWSDK.shareFacebook(this, "xxx");
```
