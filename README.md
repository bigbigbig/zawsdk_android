# zawsdk_android

## Install sdk
#### google配置
在 `app` module 下 引入 `google-services.json` 文件<br><br>
在 根目录 `build.gradle` 文件中声明 plugin
```
plugins {
    id 'com.google.gms.google-services' version '4.3.15' apply false
}
```
在 `app` module 下的 `build.gradle` 文件中使用 plugin
```
plugins {
    id 'com.google.gms.google-services'
}
```
#### facebook配置
在 `app` module `AndroidManifest.xml` 文件中添加 facebook 参数
```
<meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>
<meta-data android:name="com.facebook.sdk.ClientToken" android:value="@string/facebook_client_token"/>
```
#### sdk安装 
在 根目录`setting.gradle` 文件中添加 maven 源
```
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
```
dependencies {
    implementation 'com.zawsdk:zawsdk_android:0.3.2'
}

```


# sdk 使用
#### 初始化
```
ZAWSDK.getInstance().init(this, 渠道号, 项目 id, 授权 key, 你的隐私协议, afDevKey, Google登录的webClientId, new ResultCallback<Void>() {
			@Override
			public void onSuccess(Void var1) {
                //初始化成功后调用 quicklogin 实现静默登录
				ZAWSDK.getInstance().quickLogin(MainActivity.this, new ResultCallback<LoginModel>() {
					@Override
					public void onSuccess(LoginModel var1) {
						Log.v("SDK", "登录成功");
//						Log.v("SDK", "token" + var1.getToken());
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
```
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
```
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
```
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
```
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
```
ZAWSDK.getInstance().updateRoleInfo(sdk的用户id, 服务器 id, 服务器名称, 角色 id, 角色名称, 角色等级,
					new ResultCallback<UserRoleInfo>() {

						@Override
						public void onSuccess(UserRoleInfo data) {
							// TODO Auto-generated method stub
							if (data != null) {
								boolean result = data.getResult();
								String msg = data.getMsg();
								String uid = data.getUid();
								String role_name = data.getRole_name();
								String role_id = data.getRole_id();
								String role_level = data.getRole_level();
								Log.i("kk", "更新角色信息结果:" + result + "|msg" + msg
										+ "|role_name" + role_name + "|" + role_level
										+ "|role_id" + role_id + "|uid" + uid);

							}
						}

						@Override
						public void onFailure(int code, String var1) {

						}

					});
```

#### google 支付
```
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
