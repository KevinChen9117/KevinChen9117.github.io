---
layout: post
keywords: blog
description: blog
title: "The Model-View-Presenter pattern"
categories: [Android]
tags: [Design Pattern]
---
{% include codepiano/setup %}

You’ve most likely heard of the MVC (Model-View-Controller) pattern, and you’ve
probably used it in different frameworks. When I was trying to find a better way to
test my Android code, I learned about the MVP (Model-View-Presenter) pattern. The
basic difference between MVP and MVC is that in MVP, the presenter contains the UI
business logic for the view and communicates with it through an interface.
In this hack, I’ll show you how to use MVP inside Android and how it improves
the testability of the code. To see how it works, we’ll build a splash screen. A splash
screen is a common place to put initialization code and verifications, before the
application starts running. In this case, inside the splash screen we’ll provide a
progress bar while we’re checking whether or not we have internet access. If we do,
we continue to another activity, but if we don’t, we’ll show the user an error mes-
sage to prevent them from moving forward.
<!--more-->
To create the splash screen, we’ll have a presenter that will take care of the com-
munication between the model and the view. In this particular case, the presenter
will have two functions: one that knows when we’re online and another to take care of
controlling the view. You can see the project structure in figure 20.1.
The presenter will use a model class called ConnectionStatus that will implement
the IConnectionStatus interface. This interface will answer whether we have internet
access with a single method:
{% highlight java %}
public interface IConnectionStatus {
     boolean isOnline();
}

{% endhighlight %}
As you might be thinking, the code in charge of controlling the view will be an
Activity that implements the ISplashView interface. The interface will be used by
the presenter to control the flow of the application. Let’s look at the code for the
ISplashView interface:
{% highlight java %}
public interface ISplashView {
   void showProgress();
   void hideProgress();
   void showNoInetErrorMsg();
   void moveToMainView();
}
{% endhighlight %}
Because we’re coding in Android, the view will be the first to be created and afterward
we’ll give the control to the presenter. Let’s see how we do that:
{% highlight java %}
public class SplashActivity extends Activity implements ISplashView {
private SplashPresenter mPresenter;
@Override
public void onCreate(Bundle savedInstanceState) {
  //(1)Activity initialization code
  ...
  //(2)Instantiate presenter for this Activity
  mPresenter = new SplashPresenter();
  mPresenter.setView(this);
}
@Override
protected void onResume() {
    super.onResume();
    //(3)Start presenter code when we reach onResume() method
    mPresenter.didFinishLoading();
 }
}
{% endhighlight %}
We’ll first need to initialize the Activity (1). Afterward, we create the presenter (2)
that will take care of getting everything done and we set the Activity instance to the
presenter. We can override the onResume() method (3) to let the presenter know the
view is ready to give control to it.
The presenter code is simple. Following is the presenter’s didFinishLoading()
method:
{% highlight java %}
public void didFinishLoading() {
    //(1)Getting view, in this case the Activity
    ISplashView view = getView();
    //(2)Logic to decide if we can move on
    if (mConnectionStatus.isOnline()) {
          view.moveToMainView();
    } else {
       view.hideProgress();
       view.showNoInetErrorMsg();
   }
}
{% endhighlight %}
We’ll get a reference to the ISplashView implementation using a presenter’s getter （1）.
We’ll use the model’s IConnectionStatus implementation to verify whether we’re
online C. Depending on that, we’ll do different things with the view. As you can see,
the view is used through an interface without knowing it’s implemented by an Android
Activity. This will end up in a view that’s easy to mock in a unit test.

## 总结
Using the MVP pattern will make your code more organized and easier to test. In
the sample code, you’ll notice a test folder. The test needs to instantiate the presenter
and mock the interfaces. Because you’re not using any Android-specific code in the
presenter, you don’t need to run in an Android-powered device and instead can run it
in the JVM. In this case, you’ve used Mockito to mock the interfaces.
Because you’ve been working with Android, you’ll notice that a lot of code ends up
in the Activity. Unfortunately, testing activities is painful. Using the MVP pattern will
help you create tests and apply TDD (test-driven development) in an easy way.

