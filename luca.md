
![](https://pandao.github.io/editor.md/examples/images/8.jpg)
![](https://github.com/yaooort/Readme/blob/master/device-2017-07-21-103025.png)

#  整理
### [图标素材下载站](http://www.iconfont.cn/)

### [SVG图标动画](https://github.com/mcxtzhang/PathAnimView)

### [google登录](http://www.cnblogs.com/zhaoyanjun/p/5337442.html)

### [google 推送服务](http://justalkcloud.com/cn/docs/gitbook/account/PushNotificationIntegration/GCM/readme.html)

### [漂亮的动画 ios&android ](https://github.com/airbnb/lottie-android)
       
### google Play Billing 应用内支付
- [踩坑](http://leenjewel.github.io/blog/2014/11/21/google-play-in-app-billing-cai-guo-de-na-xie-keng/)
- [跳坑](http://leenjewel.github.io/blog/2016/06/21/google-zhi-fu-cong-ru-men-dao-tiao-keng/)
---------------
### ios IAP应用内支付
- [iOS应用内付费(IAP)提交审核一波三折](http://www.jianshu.com/p/8af97aac5e6c)
- [应用内支付IAP全部流程](http://www.jianshu.com/p/e9ae4cece800)
- [iOS应用内支付（IAP）详解](http://www.jianshu.com/p/033086546126)
- [iOS内购](http://www.jianshu.com/p/321efc31078d)
- [详细版](http://www.jianshu.com/p/847edcb1488e)
---------------
### PayPal支付
- [Android集成PayPal](http://www.jianshu.com/p/7a064f2fbfa0)
- [Android相关1](http://blog.csdn.net/ivinm/article/details/51508547)
- [Android相关2](http://www.cnblogs.com/Fredric-2013/p/4608473.html)
- [iOS](http://www.jianshu.com/p/812d9f010b5b)
- [iOS Paypal详细](http://www.devzhang.com/14664047663795.html)
### 粒子动画效果
- [Android粒子动画](https://github.com/glomadrian/Grav)
- [ios参考](http://www.jianshu.com/p/4b6d60755dd3)
### Android动画
-[Android动画封装](https://github.com/florent37/ViewAnimator)

-android luca问问动画
```java
 textView.setText("你好，欢迎使用");
        ViewAnimator.animate(textView)
                .translationX(0, -width1)
                .duration(1000)
                .alpha(1, 0)
                .onStop(new AnimationListener.Stop() {
                    @Override
                    public void onStop() {
                        textView.setText("感谢您的回复");
                        ViewAnimator.animate(textView)
                                .translationX(width1, 0)
                                .alpha(0, 1)
                                .onStart(new AnimationListener.Start() {
                                    @Override
                                    public void onStart() {
                                        ViewAnimator.animate(view)
                                                .dp().width(px2dip(MainActivity.this, width1 / 3), px2dip(MainActivity.this, width1 / 3 * 2))
                                                .duration(2000)
                                                .start();
                                    }
                                })
                                .duration(1000)
                                .start();
                    }
                })
                .start();
```

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/hello_world"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="10dip"
        android:text=""
        android:textColor="#fff"
        android:textSize="30sp" />


    <View
        android:id="@+id/line_sc"
        android:layout_width="match_parent"
        android:layout_height="3dip"
        android:background="@color/colorPrimaryDark" />

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content">


        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_margin="5dip"
            android:orientation="vertical">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="horizontal">

                <View
                    android:layout_width="3dip"
                    android:layout_height="match_parent"
                    android:layout_marginBottom="10dp"
                    android:layout_marginTop="10dip"
                    android:background="#F12047" />

                <EditText
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:textColor="#fff"
                    android:theme="@style/MyEditText" />
            </LinearLayout>

            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="right"
                android:text="发送"
                android:textColor="#fff" />
        </LinearLayout>


    </ScrollView>
</LinearLayout>

```
