# StateView

[![Build Status](https://travis-ci.org/nukc/StateView.svg?branch=master)](https://travis-ci.org/nukc/StateView)
[![Download](https://api.bintray.com/packages/nukc/maven/StateView/images/download.svg) ](https://bintray.com/nukc/maven/StateView/_latestVersion)
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-StateView-green.svg?style=true)](https://android-arsenal.com/details/1/4255)

[English](https://github.com/nukc/StateView/blob/master/README-en.md)

StateView 一个轻量级的控件, 继承自 `View`, 吸收了 `ViewStub` 的一些特性, 初始状态下是不可见的, 不占布局位置, 占用内存少。
当进行操作显示空/重试/加载视图后, 该视图才会被添加到布局中。

<img src="https://raw.githubusercontent.com/nukc/stateview/master/art/custom.gif"><img width="200"><img src="https://raw.githubusercontent.com/nukc/stateview/master/art/animations.gif">


```groovy
   compile 'com.github.nukc.stateview:library:1.5.3'

   // animator providers
   compile 'com.github.nukc.stateview:animations:1.0.1'
```

## 使用方法

直接在代码中使用:

- 注入到 Activity
```java
    mStateView = StateView.inject(Activity activity);
```

- 注入到 ViewGroup
```java
    mStateView = StateView.inject(ViewGroup parent);

    mStateView = StateView.inject(ViewGroup parent, boolean hasActionBar);
```

```java
    // 如果 View 不是 ViewGroup，则会注入到 View 的 parent 中
    mStateView = StateView.inject(View view);

    mStateView = StateView.inject(View view, boolean hasActionBar);
```

- 包裹指定的 View，这个会增加层次
```java
    mStateView = StateView.wrap(View view);
```

或添加到布局:

```xml

    <com.github.nukc.stateview.StateView
        android:id="@+id/stateView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

```

- 显示空视图: ```mStateView.showEmpty();```
- 显示加载视图: ```mStateView.showLoading();```
- 显示重试视图: ```mStateView.showRetry();```
- 显示内容: ``` mStateView.showContent();```

设置重试点击事件:

```java
    mStateView.setOnRetryClickListener(new StateView.OnRetryClickListener() {
        @Override
        public void onRetryClick() {
            //do something, no need to call showLoading()
            //不需要调用showLoading()方法, StateView自会调用
        }
    });
```

设置自定义视图:

- 全局设置办法:在自己项目的layout下新建, 名字跟StateView默认layout一样即可(也不用代码设置).
默认layout的名字:```base_empty```/```base_retry```/```base_loading```.

- 单页面设置:layout名字不一样, 然后再代码设置.

```java
    setEmptyResource(@LayoutRes int emptyResource)

    setRetryResource(@LayoutRes int retryResource)

    setLoadingResource(@LayoutRes int loadingResource)
```

利用 ```OnInflateListener``` 设置文本图像或者其它操作：
在 view 成功添加到 parent 的时候回调（每个 viewType 只回调一次）

```java
    mStateView.setOnInflateListener(new StateView.OnInflateListener() {
        @Override
        public void onInflate(@StateView.ViewType int viewType, View view) {
            if (viewType == StateView.EMPTY) {
                // set text or other
                ViewGroup emptyView = (ViewGroup) view;
                TextView tvMessage = (TextView) emptyView.findViewById(R.id.tv_message);
                ImageView ivState = (ImageView) emptyView.findViewById(R.id.iv_state);
                tvMessage.setText("custom message");
                ivState.setImageResource(R.drawable.retry);
            } else if (viewType == StateView.RETRY) {
                // ...
            }
        }
    });
```


## Custom Attribute

```xml
<resources>
    <declare-styleable name="StateView">
        <attr name="emptyResource" format="reference" />
        <attr name="retryResource" format="reference" />
        <attr name="loadingResource" format="reference" />
    </declare-styleable>
</resources>
```


## 动画切换

设置视图切换动画:

```java
    // 默认 provider 是 null，即默认不提供动画切换
    // 如果需要，设置一个就可以了
    setAnimatorProvider(AnimatorProvider provider)

```

动画效果可以自定义，也可以直接使用 animations 这个库，与主库分离，这样不需要的就可以只依赖 library。

```groovy
    compile 'com.github.nukc.stateview:animations:1.0.1'

```

目前提供了如下几个动画效果:

- 渐变缩放: ```FadeScaleAnimatorProvider```
- 卡片翻转: ```FlipAnimatorProvider```
- 左右滑动: ```SlideAnimatorProvider```


自定义的话，直接实现 ```AnimatorProvider```接口并提供 ```Animator``` 就可以了

```java
public class FadeScaleAnimatorProvider implements AnimatorProvider {

    @Override
    public Animator showAnimation(View view) {
        AnimatorSet set = new AnimatorSet();
        set.playTogether(
                ObjectAnimator.ofFloat(view, "alpha", 0f, 1f),
                ObjectAnimator.ofFloat(view, "scaleX", 0.1f, 1f),
                ObjectAnimator.ofFloat(view, "scaleY", 0.1f, 1f)
        );
        return set;
    }

    @Override
    public Animator hideAnimation(View view) {
        AnimatorSet set = new AnimatorSet();
        set.playTogether(
                ObjectAnimator.ofFloat(view, "alpha", 1f, 0f),
                ObjectAnimator.ofFloat(view, "scaleX", 1f, 0.1f),
                ObjectAnimator.ofFloat(view, "scaleY", 1f, 0.1f)
        );
        return set;
    }
}

```


## ChangeLog

[ChangeLog](https://github.com/nukc/StateView/blob/master/CHANGELOG.md) | [releases](https://github.com/nukc/StateView/releases)


## License

    The MIT License (MIT)

    Copyright (c) 2016 Nukc

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.