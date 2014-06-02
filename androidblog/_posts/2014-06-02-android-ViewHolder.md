---
layout: post
keywords: blog
description: blog
title: "Android ViewHolder Pattern"
categories: [Android]
tags: [Android 源码解析及应用]
---
{% include codepiano/setup %}

 * Viewholder模式在Adapter中有广泛的应用， 一般的写法是在Adapter类中写一个静态内部类，
 * 里面包含几个静态成员变量。这里介绍的写法很酷。 但是执行效果跟普通做法基本一致。本类中用到了
 * 效率比较高的集合类SparseArray.也是Android里 推荐用它类替换HashMap<Integer,Object>。
<!--more-->
{% highlight java %}
package com.kevincode;
import android.util.SparseArray;
import android.view.View;
/**
 * Viewholder模式在Adapter中有广泛的应用， 一般的写法是在Adapter类中写一个静态内部类，
 * 里面包含几个静态成员变量。这里介绍的写法很酷。 但是执行效果跟普通做法基本一致。本类中用到了
 * 效率比较高的集合类SparseArray.也是Android里 推荐用它类替换HashMap<Integer,Object>。
 * 
 * @author Kevin
 * 
 */
public class ViewHolder {
	/**
	 * 通过该方法去获取ViewHolder中的子View.
	 * @param view Adapter中的View，一般是getView方法的第二个参数convertView
	 * @param id 子View的id.
	 * @return 返回子View.
	 */
	@SuppressWarnings("unchecked")
	public static <T extends View> T get(View view, int id) {
		SparseArray<View> viewHolder = (SparseArray<View>) view.getTag();
		if (viewHolder == null) {
			//创建一个SparseArray对象来保存ViewHolder模式需要Hold的View
			viewHolder = new SparseArray<View>();
			//将SparseArray对象跟指定View绑定。
			view.setTag(viewHolder);
		}
		//从SparseArray对象里获取子View.
		View childView = viewHolder.get(id);
		//如果没有找到，则通过指定的View去获取子View,
		//然后将它放进SparseArray中，下次获取。
		if (childView == null) {
			childView = view.findViewById(id);
			viewHolder.put(id, childView);
		}
		return (T) childView;
	}
}
{% endhighlight %}