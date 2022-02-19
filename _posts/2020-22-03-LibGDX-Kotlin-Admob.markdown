---
layout: post
title:  "Setting up LibGDX, Kotlin and Admob (with Android)"
date:   2020-03-22 20:00:00 +0100
categories: programming android functional kotlin libgdx admob
---
Hello everybody,

As regular readers of my blog know, I love [functional programming][fp]. And I love [Android app programming][ode], and I hope you do too.  
While programming my latest Android game [Jewel Trop][app], I had several problems adding ads which I couldn't solve immediately. For example, libGDX genereates *three* different `build.gradle` files, so good luck finding the right one for your dependencies.  
For some of these problems I couldn't find direct help in the Internet, so I decided to share this step-by-step guide to walk you through all of solutions and save you some time.  
If you don't care about the details and just about the code, then go straight to my [GitHub project][git] where I added all the code combined in an unchanged libGDX Project.

Without further ado, let's do this!

## Adding Android Admob/Firebase-Ads Dependency


To add support for Admob Ads in your app, open the `android/build.gradle` file (in your `android` folder) and add the following new dependency block at the very end of the file:
<pre>
...
}

eclipse.project.name = appName + "-android"

<b>dependencies {</b>
<b>implementation 'com.google.firebase:firebase-ads:19.0.1'</b>
<b>}</b>
</pre>

## Enabling Android X

After you try to compile your app now, an unpleasant error will be displayed: 

```
This project uses AndroidX dependencies, but the 'android.useAndroidX' property is not enabled. Set this property to true in the gradle.properties file and retry.
The following AndroidX dependencies are detected: androidx.versionedparcelable:versionedparcelable:1.0.0, androidx.slidingpanelayout:slidingpanelayout:1.0.0, androidx.fragment:fragment:1.0.0, androidx.core:core:1.0.0, androidx.customview:customview:1.0.0, androidx.swiperefreshlayout:swiperefreshlayout:1.0.0, androidx.interpolator:interpolator:1.0.0, androidx.loader:loader:1.0.0, androidx.drawerlayout:drawerlayout:1.0.0, androidx.viewpager:viewpager:1.0.0, androidx.collection:collection:1.0.0, androidx.localbroadcastmanager:localbroadcastmanager:1.0.0, androidx.lifecycle:lifecycle-common:2.0.0, androidx.arch.core:core-common:2.0.0, androidx.annotation:annotation:1.0.0, androidx.legacy:legacy-support-core-ui:1.0.0, androidx.lifecycle:lifecycle-livedata:2.0.0, androidx.lifecycle:lifecycle-viewmodel:2.0.0, androidx.browser:browser:1.0.0, androidx.lifecycle:lifecycle-livedata-core:2.0.0, androidx.arch.core:core-runtime:2.0.0, androidx.legacy:legacy-support-core-utils:1.0.0, androidx.documentfile:documentfile:1.0.0, androidx.cursoradapter:cursoradapter:1.0.0, androidx.lifecycle:lifecycle-runtime:2.0.0, androidx.coordinatorlayout:coordinatorlayout:1.0.0, androidx.asynclayoutinflater:asynclayoutinflater:1.0.0, androidx.print:print:1.0.0
```

To solve this issue, open the `gradle.properties` file in your root folder and add the following line to enable Android X:


<pre>
org.gradle.daemon=true
org.gradle.jvmargs=-Xms128m -Xmx1500m
org.gradle.configureondemand=false
<b>android.useAndroidX=true</b>
</pre>

### Adding an AdView to your Project

Now you can add the Admob app ID in your `android/AndroidManifest.xml` file and an AdView to your `AndroidLauncher`. The minimal example for this is:

android/AndroidManifest.xml:

<pre>
      </activity>
      <b><meta-data</b>
            <b>android:name="com.google.android.gms.ads.APPLICATION_ID"</b>
            <b>android:value="ca-app-pub-3940256099942544~3347511713" /></b>
     </application>
 
 </manifest>
</pre>

android/.../AndroidLauncher.java:
<pre>
...
 public class AndroidLauncher extends AndroidApplication {
        @Override
        protected void onCreate (Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                AndroidApplicationConfiguration config = new AndroidApplicationConfiguration();
                <b>AdView a = new AdView(this);</b>
                initialize(new MyGdxGame(), config);
      }
...
</pre>

### Increase Minimum Android SDK Version

Compiling now gives you the following error message:

```
Manifest merger failed : uses-sdk:minSdkVersion 14 cannot be smaller than version 16 declared in library [com.google.firebase:firebase-ads:19.0.1] .../firebase-ads-19.0.1/AndroidManifest.xml as the library might be using APIs not available in 14
	Suggestion: use a compatible library with a minSdk of at most 14,
		or increase this project's minSdk version to at least 16,
		or use tools:overrideLibrary="com.google.firebase.firebase_ads" to force usage (may lead to runtime failures)
```

So we need to increase the minimal Android Version 16 (Android 4.1 Jelly Bean). This can be done easily in the `android/build.gradle` file:

<pre>
     defaultConfig {
        applicationId "com.mygdx.game"
        <b>minSdkVersion 16</b> //From 14
        targetSdkVersion 29
        ...
</pre>


If your app compiles without any errors by now, congratulations. But for all the developers who have errors while using other big dependencies other then Admob, don't worry, I've got you covered with *multiple DEX files*.


## Too Many Classes / Multidex

The last problem on our journey is appears when we try to compile now:

```
Cannot fit requested classes in a single dex file (# methods: 93902 > 65536)
```

With all of our dependencies (in my case the amazing functional programming library [Arrow][arrow]) we just have too many classes in our app for *one* DEX file.

The solution is to add support for multiple DEX files in our app. Therefore, we add and enable the multi dex dependency in our `android/build.gradle` file:

<pre>
...
 defaultConfig {
        applicationId "com.mygdx.game"
        minSdkVersion 16
        targetSdkVersion 29
        versionCode 1
        versionName "1.0"
        <b>multiDexEnabled true</b>
    }
...
</pre>

and

<pre>
dependencies {
    implementation 'com.google.firebase:firebase-ads:19.0.1'
    <b>implementation 'com.android.support:multidex:1.0.3'</b>
}
</pre>

once re-compiled, we get the following (and last) error message:

```
More than one file was found with OS independent path 'androidsupportmultidexversion.txt'
```

Because this txt file is there more than once, we have to exclude it in the `android/build.gradle` file:

<pre>
...
packagingOptions {
    exclude 'META-INF/robovm/ios/robovm.xml'
    <b>exclude 'androidsupportmultidexversion.txt'</b>
}
...
</pre>

As you will notice, your app will now compile without any error and your good to go. Congratulations, you've done it!

## Conclusion

That's it for today!

We have fixed all nasty problems towards showing ads in your own libGDX game. We enabled Android X and manifest merging for the ad dependency and multiple DEX files for all our other dependencies.  
You can find all of the code combined in a vanilla libGDX Project on [GitHub][git].

From here on, you can follow the wonderful [guide by libGDX itself][wiki] on how to display ads in your app or the great [guide by Cansu Yeksan Aktaş][medium].

Please leave a comment if this post helped you or if you have any questions regarding this blog post.

Thanks for reading and have a nice day,

Niklas

[medium]: https://medium.com/swlh/adding-admob-banner-and-interstitial-ads-to-your-android-app-step-by-step-from-scratch-8505d02a09d8#8d7a
[wiki]: https://github.com/libgdx/libgdx/wiki/Admob-in-libgdx
[arrow]: https://arrow-kt.io/
[fp]: https://flyingbytes.github.io/programming/java8/functional/part0/2017/01/16/Java8-Part0.html
[ode]: https://flyingbytes.github.io/programming/android/2017/11/06/Android-Ode.html
[app]: https://play.google.com/store/apps/details?id=de.nwuensche.jeweltrop&hl=gsw
[git]: https://github.com/NWuensche/AdsLibGDXKotlin
