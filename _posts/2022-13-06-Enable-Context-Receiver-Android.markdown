---
layout: post
title:  "Enable Kotlin Context Receivers in an Android Project"
date:   2022-06-13 19:00:00 +0100
categories: programming android kotlin context receiver contextreceiver kotlin17 kotlin1620
---
Hello everybody,

As some of you know, [Context Receivers][CR] are a new, unstable feature added in Kotlin 1.6.20. I find them really interesting, as they are a syntactical generalization of extension functions (although extension functions and context receiver are used in different semantic cases, see [this video][CRVid] for an explanation). 

As I went along trying out Context Receivers, I found it rather difficult to enable them for toying around.
Unfortunately, I am missing the usual step-by-step tutorial by JetBrains. They only tell us that we have to [add a compile flag][CF], but this does not help much. And most blog posts skip this step as well. Even Stack Overflow does not help this time! This blog post should help everyone who has the same problem enabling Context Receivers without wasting as much time as I did searching for the necessary actions.

Of course, all of this could change dramatically when the feature becomes stable over time. This guide is from June 2022, and I use Android Studio Chipmunk as my IDE.

## 0. Add Context Receiver to Code

Before we enable Context Receivers, we add the following class somewhere in our app:

```kotlin
context(Unit)
class Test()
```

To make this compile, we have to do the following two actions:

## 1. Update Kotlin Language Features

To fix the "Expecting a top level declaration" compile error, first, we have to enable the language features of Kotlin 1.6.20 (or higher). We do this by updating the used Kotlin version inside the `build.gradle` file of the project folder:

```groovy
plugins {
    ...
    id 'org.jetbrains.kotlin.android' version '1.6.20' apply false #Or anything higher than 1.7.0
}
```

This fixes the error.

## 2. Enable Context Receivers

To fix the "The feature 'context receivers' is experimental and should be enabled explicitly" compile error, we have to set the following compile flag inside the `build.gradle` file of the `app` module folder:


```groovy
android {
  kotlinOptions {
    ...
    freeCompilerArgs += "-Xcontext-receivers"
  }
}
```

Now, the code compiles. Congratulations!

## Conclusion

That's it for today!

You have seen the necessary steps to enable Context Receivers in any Android app. 

Unfortunately, Android Studio (Chipmunk) still shows a syntax error when using Context Receivers. However, the code still compiles and works. Do you know any way to remove this error? If so, please present it in the comment section below.

Please leave a comment if this post helped you or if you have any questions regarding this blog post.


Thanks for reading and have a nice day,

Niklas


[CR]: https://blog.jetbrains.com/kotlin/2022/02/kotlin-1-6-20-m1-released/
[CRVid]: https://www.youtube.com/watch?v=GISPalIVdQY
[CF]: https://github.com/Kotlin/KEEP/blob/master/proposals/context-receivers.md#prototype
