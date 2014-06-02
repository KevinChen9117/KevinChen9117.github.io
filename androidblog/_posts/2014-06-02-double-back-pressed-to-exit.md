---
layout: post
keywords: blog
description: blog
title: "Double back press to exit"
categories: [Android]
tags: [Android 代码片段]
---
{% include codepiano/setup %}

 Android 里常用的一个设计是点击两次Back键退出应用或Activity功能，避免误操作。
 这种设计很贴心，实现也比较简单。
<!--more-->
{% highlight java %}
	//一个静态变量用于保存按下时的系统时间
private static long mBackPressed = 0;
@Override
public void onBackPressed() {
	//连续两次按下，切时间间隔在两秒内，才会满足该条件。
	if(mBackPressed+2000 > System.currentTimeMillis()){
		super.onBackPressed();
	}else{
		//第一次按下时，弹出Toast消息。
		Toast.makeText(getApplicationContext(), 
		              "Press once again to exit !", Toast.LENGTH_SHORT).show();
		//将系统时间设置给mBackPressed变量，
		//两秒内再次按下的时候才会满足if条件。
		mBackPressed = System.currentTimeMillis();
	}
}
{% endhighlight %}