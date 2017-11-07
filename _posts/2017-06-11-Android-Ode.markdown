---
layout: post
title:  "An Ode to Android"
date:   2017-11-06 13:00:00 +0100
categories: programming android
---
Let's face it: I'm an Android fanboy.

The Android Platform is one of my favourite, if not my most favourite, platform to develop for. 

I want to tell you exactly why I think Google makes a good job regarding Android. Nevertheless, I also try to highlight some problems I have with Android, because be realistic: Nothing's perfect.

## The Development Enviroment

Back in the days, Android Development in Eclipse was such a paint. The IDE just wasn't meant to be used for job of developing apps. Plugin Managment was painful, the UI preview didn't really work and everything crashed most of the time (at least for me).

However, Android Studio was developed to solve these problems. And it does so to an almost perfect extend. You can tell that Goole keeps an eye on feedback and integrates every new tool nicely into the IDE (Kotlin, Espresso Test Recorder, ...). The development of the app's UI is just as fast and smooth as the creation of business logic. It just feels right doing all of this. Developing Android Apps is such a pleasure nowadays.

## The Language

I like Java very much. In my opinion, static typing makes much more sense in big applications than dynamic typing. But I also like the design decisions behind the syntax. The functional programming techniques, which were introduced in Java 8, are a implemented nice into Java's ecosystem. If you want to learn more about them, check out my [other posts][blog].

Everytime I read or work with Android Code, the language doesn't get in my way. It just makes sense and is easy to understand.

## The Bug Bounty System

Breaking other peoples code is a real pleasure for me ;) . Getting inside a developer's mind and exploiting common code habits can can keep me busy for a whole weekend. And [bug bounty reports][bug] are by far the most facinating blog posts for me.

However, this task is even more rewarding now, because Google expanded it's [bug bounty program][bounty] onto some Android Apps last month. Now, you can make real money reporting issues you found and learn so much about the Android system while doing this.

## The Testing Enviroment

As a notorious tester, I want to be save that everything is well tested and works as expected. I don't want to release buggy apps. Android makes it so easy to do so. Espresso and Rebolectric are so intuitive and just **work**. And if you also use Kotlin in your projects, it's easy to make your tests even more readable with extension functions.

It just feels right to write tests. This is so important in an enviroment where few people practice TDD and just rely on luck to find bugs.

## Contributing
When I'm working on a new project, it's always easy to get started. The amazing work Google does at documanting Android features is one of the best reasons for that.

However, everytime I have a problem, there is someone in an IRC chat or on a email list who can give me a quick answer. On other platforms I've developed for, I haven't got this kind intimate feeling in other communities.

---

But of course, not everything's good about Android. There are still problems which this platform has and which simply annoy me as a developer

## New year = New Android Version

Let's be clear: It's awesome when a platform develops over time. But maybe Google should give us the time to really embrace new features.

Not even a [quarter][Users] of the Android users use Android N right now. Still, there is Orea coming out right now. 

You cannot use all of the new feautres and upgrade the minimum SDK Version of your app. This is because most of the users don't buy a new phone with the latest Android version every year. Also, phone manifactures usally don't provide new Android versions for their older phones. And not everybody wants to use a custom OS on their devices.

You can see this rapid development also in Android books which come out. They're already outdated the day they come out.

Google should just give the developers and users more time to adapt a new Android Version.

## Conclusion

To sum everything up: The Android platform is an superb plaform for developers. Google provides an awesome IDE and documentation which doesn't leave anyone behind. The community is amazing as well.

However, the System's not perfect. Google should slow down the release cycle so that the developers and users can adapt better to every new Android version.

What's your opinion on all of this? Which parts of the Android ecosystem do you love and hate? Please write a comment and check out my other blog posts too!

Thanks for reading and have a nice day

Niklas

<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = '//flyingbytes.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>

<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>

[Users]: https://www.statista.com/statistics/271774/share-of-android-platforms-on-mobile-devices-with-android-os/
[blog]: https://flyingbytes.github.io/programming/java8/functional/part0/2017/01/16/Java8-Part0.html
[bug]: https://medium.freecodecamp.org/messing-with-the-google-buganizer-system-for-15-600-in-bounties-58f86cc9f9a5
[bounty]: https://www.google.com/about/appsecurity/android-rewards/
