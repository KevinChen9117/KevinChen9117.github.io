---
layout: post
keywords: blog
description: blog
title: "Android Intents"
categories: [Android]
tags: [Android 开源库]
---
{% include codepiano/setup %}

在这里，会收集一些有关Intent的代码片段，一些开源代码注释。
<!--more-->
##Android Intents 

###EmailIntents.java
{% highlight java %}
/**
 * Provides factory methods to create intents to send emails
 * 提供简单的工厂方法创建发送邮件的Intent.
 * 
 */
public class EmailIntents {

    /**
     * Create an intent to send an email to a single recipient
     *
     * @param address The recipient address (or null if not specified)
     * @param subject The subject of the email (or null if not specified)
     * @param body    The body of the email (or null if not specified)
     * @return the intent
     */
    public static Intent newEmailIntent(String address, String subject, String body) {
        return newEmailIntent(address, subject, body, null);
    }

    /**
     * Create an intent to send an email with an attachment to a single recipient
     *
     * @param address    The recipient address (or null if not specified)
     * @param subject    The subject of the email (or null if not specified)
     * @param body       The body of the email (or null if not specified)
     * @param attachment The URI of a file to attach to the email. Note that the URI must point to a location the email
     *                   application is allowed to read and has permissions to access.
     * @return the intent
     */
    public static Intent newEmailIntent(String address, String subject, String body, Uri attachment) {
        return newEmailIntent(address == null ? null : new String[]{address}, subject, body, attachment);
    }

    /**
     * Create an intent to send an email with an attachment
     *
     * @param addresses  The recipients addresses (or null if not specified)
     * @param subject    The subject of the email (or null if not specified)
     * @param body       The body of the email (or null if not specified)
     * @param attachment The URI of a file to attach to the email. Note that the URI must point to a location the email
     *                   application is allowed to read and has permissions to access.
     * @return the intent
     */
    public static Intent newEmailIntent(String[] addresses, String subject, String body, Uri attachment) {
        Intent intent = new Intent(Intent.ACTION_SEND);
        if (addresses != null) intent.putExtra(Intent.EXTRA_EMAIL, addresses);
        if (body != null) intent.putExtra(Intent.EXTRA_TEXT, body);
        if (subject != null) intent.putExtra(Intent.EXTRA_SUBJECT, subject);
        if (attachment != null) intent.putExtra(Intent.EXTRA_STREAM, attachment);
        intent.setType(MIME_TYPE_EMAIL);

        return intent;
    }
	
	//接收者主要是通过Action 和 MIME Type进行过滤
    private static final String MIME_TYPE_EMAIL = "message/rfc822";
}
{% endhighlight %}

###GeoIntents.java
{% highlight java %}
/**
 * Provides factory methods to create intents to work with geographical data (search locations for instance)
 * 提供简单的工厂方法，用于创建跟地理数据打交道（如查询位置）的Intents.
 */
public class GeoIntents {

    /**
     * Intent that should allow opening a map showing the given address (if it exists)
     * 打开地图并显示指定地理位置（如果存在）的Intent。
	 *
     * @param address    The address to search
     * @param placeTitle The title to show on the marker
     * @return the intent
     */
    public static Intent newMapsIntent(String address, String placeTitle) {
        StringBuilder sb = new StringBuilder();
        sb.append("geo:0,0?q=");

        String addressEncoded = Uri.encode(address);
        sb.append(addressEncoded);

        // pass text for the info window
        String titleEncoded = Uri.encode("(" + placeTitle + ")");
        sb.append(titleEncoded);

        // set locale; probably not required for the maps app?
        sb.append("&hl=" + Locale.getDefault().getLanguage());

        return new Intent(Intent.ACTION_VIEW, Uri.parse(sb.toString()));
    }
}
{% endhighlight %}

###MediaIntents.java
{% highlight java %}
/**
 * Provides factory methods to create intents to view / open / ... medias
 * 提供简单的工厂方法用于打开多媒体文件
 * 
 */
public class MediaIntents {

    /**
     * Open the video player to play the given
     *  通过指定地址，打开视频播放器的Intent。
	 *
     * @param url The URL of the video to play.
     * @return the intent
     */
    public static Intent newPlayVideoIntent(String url) {
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setDataAndType(Uri.parse(url), "video/*");
        return intent;
    }

    /**
     * Creates an intent that will launch a browser (most probably as other apps may handle specific URLs, e.g. YouTube)
     * to view the provided URL.
     *
     * @param url the URL to open
     * @return the intent
     */
    public static Intent newOpenWebBrowserIntent(String url) {
        if (!url.startsWith("https://") && !url.startsWith("http://")) {
            url = "http://" + url;
        }
        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
        return intent;
    }

    /**
     * Creates an intent that will launch the camera to take a picture that's saved to a temporary file so you can use
     * it directly without going through the gallery.
     *
     * @param tempFile the file that should be used to temporarily store the picture
     * @return the intent
     */
    public static Intent newTakePictureIntent(File tempFile) {
        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        intent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(tempFile));
        return intent;
    }

    /**
     * Creates an intent that will launch the camera to take a picture that's saved to a temporary file so you can use
     * it directly without going through the gallery.
     *
     * @param tempFile the file that should be used to temporarily store the picture
     * @return the intent
     */
    public static Intent newTakePictureIntent(String tempFile) {
        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        intent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(new File(tempFile)));
        return intent;
    }

    /**
     * Creates an intent that will launch the phone's picture gallery to select a picture from it.
     *
     * @return the intent
     */
    public static Intent newSelectPictureIntent() {
        Intent intent = new Intent(Intent.ACTION_PICK);
        intent.setType("image/*");
        return intent;
    }
}
{% endhighlight %}

###PhoneIntents.java
{% highlight java %}
/**
 * Provides factory methods to create intents to send SMS, MMS and call phone numbers
 * 跟电话相关的Intents.
 */
public class PhoneIntents {

    /**
     * Creates an intent that will allow to send an SMS without specifying the phone number
     *
     * @param body The text to send
     * @return the intent
     */
    public static Intent newSmsIntent(String body) {
        return newSmsIntent(null, body);
    }

    /**
     * Creates an intent that will allow to send an SMS to a phone number
     *
     * @param phoneNumber The phone number to send the SMS to (or null if you don't want to specify it)
     * @param body        The text to send
     * @return the intent
     */
    public static Intent newSmsIntent(String phoneNumber, String body) {
        final Intent intent;
        if (phoneNumber == null || phoneNumber.trim().length() <= 0) {
            intent = new Intent(Intent.ACTION_VIEW, Uri.parse("sms:"));
        } else {
            intent = new Intent(Intent.ACTION_VIEW, Uri.parse("sms:" + phoneNumber));
        }
        intent.putExtra("sms_body", body);
        return intent;
    }

    /**
     * Creates an intent that will open the phone app and enter the given number. Unlike
     * {@link #newCallNumberIntent(String)}, this does not actually dispatch the call, so it gives the user a chance to
     * review and edit the number.
     *
     * @param phoneNumber the number to dial
     * @return the intent
     */
    public static Intent newDialNumberIntent(String phoneNumber) {
        final Intent intent;
        if (phoneNumber == null || phoneNumber.trim().length() <= 0) {
            intent = new Intent(Intent.ACTION_DIAL, Uri.parse("tel:"));
        } else {
            intent = new Intent(Intent.ACTION_DIAL, Uri.parse("tel:" + phoneNumber.replace(" ", "")));
        }
        return intent;
    }

    /**
     * Creates an intent that will immediately dispatch a call to the given number. NOTE that unlike
     * {@link #newDialNumberIntent(String)}, this intent requires the {@link android.Manifest.permission#CALL_PHONE}
     * permission to be set.
     *
     * @param phoneNumber the number to call
     * @return the intent
     */
    public static Intent newCallNumberIntent(String phoneNumber) {
        final Intent intent;
        if (phoneNumber == null || phoneNumber.trim().length() <= 0) {
            intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:"));
        } else {
            intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:" + phoneNumber.replace(" ", "")));
        }
        return intent;
    }
}
{% endhighlight %}

###ShareIntents.java
{% highlight java %}
/**
 * Provides factory methods to create intents to share stuff
 * 创建用于分享事物的Intents
 *
 */
public class ShareIntents {

    /**
     * Creates a chooser to share some data.
     *
     * @param subject            The subject to share (might be discarded, for instance if the user picks an SMS app)
     * @param message            The message to share
     * @param chooserDialogTitle The title for the chooser dialog
     * @return the intent
     */
    public static Intent newShareTextIntent(String subject, String message, String chooserDialogTitle) {
        Intent shareIntent = new Intent(Intent.ACTION_SEND);
        shareIntent.putExtra(Intent.EXTRA_TEXT, message);
        shareIntent.putExtra(Intent.EXTRA_SUBJECT, subject);
        shareIntent.setType(MIME_TYPE_TEXT);
        return Intent.createChooser(shareIntent, chooserDialogTitle);
    }

    private static final String MIME_TYPE_TEXT = "text/*";
}
{% endhighlight %}

###MarketIntents.java
{% highlight java %}

/**
 * Provides factory methods to create intents to do some system tasks such as opening the market app, ...
 * 用于创建跟应用市场有关的Intents.
 * 
 */
public class MarketIntents {

    /**
     * Intent that should open the app store of the device on the current application page
     *
     * @param context The context associated to the application
     * @return the intent
     */
    public static Intent newMarketForAppIntent(Context context) {
        String packageName = context.getApplicationContext().getPackageName();
        return newMarketForAppIntent(context, packageName);
    }

    /**
     * Intent that should open the app store of the device on the given application
     *
     * @param context     The context associated to the application
     * @param packageName The package name of the application to find on the market
     * @return the intent or null if no market is available for the intent
     */
    public static Intent newMarketForAppIntent(Context context, String packageName) {
        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("market://details?id=" + packageName));

        if (!IntentUtils.isIntentAvailable(context, intent)) {
            intent = new Intent(Intent.ACTION_VIEW, Uri.parse("amzn://apps/android?p=" + packageName));
        }

        if (!IntentUtils.isIntentAvailable(context, intent)) {
            intent = null;
        }

        if (intent != null) {
            intent.addFlags(Intent.FLAG_ACTIVITY_NO_HISTORY | Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET);
        }

        return intent;
    }

    /**
     * Intent that should open either the Google Play app or if not available, the web browser on the Google Play website
     *
     * @param context     The context associated to the application
     * @param packageName The package name of the application to find on the market
     * @return the intent for native application or an intent to redirect to the browser if google play is not installed
     */
    public static Intent newGooglePlayIntent(Context context, String packageName) {
        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("market://details?id=" + packageName));

        if (!IntentUtils.isIntentAvailable(context, intent)) {
            intent = MediaIntents.newOpenWebBrowserIntent("https://play.google.com/store/apps/details?id="
                    + packageName);
        }

        if (intent != null) {
            intent.addFlags(Intent.FLAG_ACTIVITY_NO_HISTORY | Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET);
        }

        return intent;
    }

    /**
     * Intent that should open either the Amazon store app or if not available, the web browser on the Amazon website
     *
     * @param context     The context associated to the application
     * @param packageName The package name of the application to find on the market
     * @return the intent for native application or an intent to redirect to the browser if google play is not installed
     */
    public static Intent newAmazonStoreIntent(Context context, String packageName) {
        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("amzn://apps/android?p=" + packageName));

        if (!IntentUtils.isIntentAvailable(context, intent)) {
            intent = MediaIntents.newOpenWebBrowserIntent("http://www.amazon.com/gp/mas/dl/android?p="
                    + packageName);
        }

        if (intent != null) {
            intent.addFlags(Intent.FLAG_ACTIVITY_NO_HISTORY | Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET);
        }

        return intent;
    }
}

{% endhighlight %}

###IntentUtils.java
{% highlight java %}
/**
 * Provides utility functions to work with intents
 * Intents的工具类。
 * 
 */
public class IntentUtils {

    /**
     * Checks whether there are applications installed which are able to handle the given action/data.
     *
     * @param context  the current context
     * @param action   the action to check
     * @param uri      that data URI to check (may be null)
     * @param mimeType the MIME type of the content (may be null)
     * @return true if there are apps which will respond to this action/data
     */
    public static boolean isIntentAvailable(Context context, String action, Uri uri, String mimeType) {
        final Intent intent = (uri != null) ? new Intent(action, uri) : new Intent(action);
        if (mimeType != null) {
            intent.setType(mimeType);
        }
        List<ResolveInfo> list = context.getPackageManager().queryIntentActivities(intent,
                PackageManager.MATCH_DEFAULT_ONLY);
        return !list.isEmpty();
    }

    /**
     * Checks whether there are applications installed which are able to handle the given action/type.
     *
     * @param context  the current context
     * @param action   the action to check
     * @param mimeType the MIME type of the content (may be null)
     * @return true if there are apps which will respond to this action/type
     */
    public static boolean isIntentAvailable(Context context, String action, String mimeType) {
        final Intent intent = new Intent(action);
        if (mimeType != null) {
            intent.setType(mimeType);
        }
        List<ResolveInfo> list = context.getPackageManager().queryIntentActivities(intent,
                PackageManager.MATCH_DEFAULT_ONLY);
        return !list.isEmpty();
    }

    /**
     * Checks whether there are applications installed which are able to handle the given intent.
     *
     * @param context the current context
     * @param intent  the intent to check
     * @return true if there are apps which will respond to this intent
     */
    public static boolean isIntentAvailable(Context context, Intent intent) {
        List<ResolveInfo> list = context.getPackageManager().queryIntentActivities(intent,
                PackageManager.MATCH_DEFAULT_ONLY);
        return !list.isEmpty();
    }
}
{% endhighlight %}