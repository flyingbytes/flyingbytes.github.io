---
layout: post
title:  "An Ode to Android"
date:   2017-11-06 13:00:00 +0100
categories: programming android
---
Let's face it: I'm an Android fanboy.

The Android Platform is one of my favourite, if not my most favourite, platform to develop for. 

I want to tell you exaclty why I think Google makes a good job regarding Android. Nevertheless, I want to highlight some problem too, because be realistic: Nobody's perfect.

## The Development Enviroment

Back in the days, Android Development in Eclipse was a paint. This IDE wasn't meant to be used for this particular job. Plugin Managment was muehsam, the Smartphone emulators were exremely slow and everything crashed most of the time. 

However, Android Studio is meant to do this job to a almost perfect . You can tell that Goole keeps an eye on feedback and integrates every new tool nicely into the IDE (Kotlin, Espresso Test Ding, ...). The UI development is just as fast and smooth as the development of business logic. It just feels right. Developing Android Apps is such a pleasure nowadays.

## The Language

I like Java very much. In my opinion, static typing makes much more sense in big applications than dynamic typing. But I also like the design decisions behind the syntax. The functional programming techniques, which were introduced in Java 8, are a implemented nice into Java's ecosystem. If you want to learn more about them, check out my [other posts][blog].

Everytime I read or work with Android Code, the language doesn't get in my way. It just makes sense and is easy to understand.

## The Bug Bounty System

Breaking other peoples code is a real pleasure for me. Getting inside a developers mind and exploiting common code habits can can keep me busy for a whole weekend. And [bug bounty reports][bug] are by far the most facinating blog posts for me.

However, this task is even more rewarding now, because Google expanded it's [bug bounty program][bounty] onto some Android Apps last month. You can make real money now reporting issues you found and learn so much about the Android system while doing this.

## The Testing Enviroment

As a notorious tester, I want to be save that everythin is well tested and works as expected. Android makes it so easy to do so. Espresso and Rebolectric are so intuitive and they just **work**. If you throw Kotlin also in this pod, you can make your tests even more readable with extension functions.

It just feels right to write tests. This is so good in an enviroment where few people practice TDD and just hope on their luck to find bugs.

## The community
When I'm working on a new project, it's always easy to get started. The amazing work Google does at documanting Android features is one of the best reasons for that.

However, everytime I have a problem, there is someone in an IRC chat or email list who can give me a quick answer. On other platforms I developed for, I haven't got this kind welcomed feeling

But of course, not everything's good about Android. There are still problems which this platform has and which simply annoy me as a developer

## New year = New Android Version

Let's be clear: It's awesome when a platform develops over time. But maybe Google should gibe us the time to really embrace new features.

Not even a [quarter][Users] the Android users use Android N right now. Still, there is Orea coming up right now. 

There is a reason why I don't buy books about Android development. They're already outdated the day they come out.

You cannot use all of the new feautres and upgrade the minimum SDK Version of your app. This is because most of the users aren't buying a new phone with the latest Android version every year. Phone Manifactures don't provide new Android Versions to their old and low-budget phones. 

Google should just give the developers and users more time to adapt a new Android Version.

## Conclusion

To sum everything up: The Android platform is an superb plaform for developers. oogle provides an awesome IDE and documentation. The community is amazing too.

However, it's not perfect. Google should slow the release cycle down so that the developers and users can adapt better to every new Android Version.


In the [last parts][part3], we've learned about the most basic ways to program functional in Java. We used functions as Objects, Streams and so much more.

Today, we want to create a useful example with them. You will train the paradigms of the last parts and learn how the optimize the run time of your program.

## Example: Split a Stream by a specific filter

You might have stumbled over a problem like this before: You have a collection of objects and want to split them by a specific filter. After that, you want to perform some action with all elements who passed the test and another action with the ones that didn't passed the test.

### The Basic (and Slow) Approach

{% highlight java %}
public <T> void splitAndPerform(Collection<T> items, Predicate<T> splitBy, Consumer<T> passed, Consumer<T> notPassed) {
    items.stream()
        .filter(splitBy)
        .forEach(passed);

    items.stream()
        .filter(splitBy.negate())
        .forEach(notPassed);
}
{% endhighlight %}

This approach works, but it is pretty slow. The run time for splitting alone is O(2n), because we go through the whole collection 2 times: Ones to get all the items that passed the test and ones to get all the items that didn't passed it.

But what if we would create a splitter on our own? It'd sort all items in the collection, depending on whether or not they pass the test, into one List or the other. This would lower the run time to split the collection to O(n), because you just have to call filter once, not twice.

So let's do this.

## The Better Approach
### 1. Split the Collection

The first step to building our splitter is to, you guessed it, split it.

In our `splitBy()` function, we want to take a `Predeciate<T>` as a parameter and return a new `Splitter` object, which is basically an object which consists of two Lists. One List consists of all the objects that passed the test, and the other one of all the objects that didn't pass the test.

{% highlight java %}
public class Splitter<T> {

    private List<T> passed;
    private List<T> notPassed;

    private Splitter(List<T> passed, List<T> notPassed) {
        this.passed = passed;
        this.notPassed = notPassed;
    }

    public static <T> Splitter<T> splitBy(Collection<T> items,Predicate<T> test) {
        List<T> passed = new LinkedList<T>();
        List<T> notPassed = new LinkedList<T>();

        items.stream()
                .forEach(item -> {
                    if(test.test(item)){
                        passed.add(item);
                        return;
                    }
                    notPassed.add(item);
                });

        return new Splitter<T>(passed, notPassed);
    }

}
{% endhighlight %}

As you can see, we used the factory method pattern to make the creation of the Splitter nicer.

Now that we can create a Splitter object, we want to give it some functionality.

### 2. Work With the Split Lists

We want to work with the Lists in the same way that we can work with Streams. But we don't want to recreate every function that a Stream has for our two lists. That's where a nice design pattern comes in handy. I heard about it in this [talk][talk] and it's a very cool way to use lambdas. We basically create two new functions, called `workOnPassedItems` and `workOnNotPassedItems`. They take a `Consumer<Stream<T>>`. Therefore, we can create a lambda and work inside it with a normal looking stream. This method will then be applied onto the List of passed and not passed items.

{% highlight java %}
public class Splitter<T> {

    //...

    public Splitter<T> workWithPassed(Consumer<Stream<T>> func) {
        func.accept(passed.stream());
        return this;
    }

    public Splitter<T> workWithNotPassed(Consumer<Stream<T>> func) {
        func.accept(notPassed.stream());
        return this;
    }

}
{% endhighlight %}

We used the cascade design pattern to make the use of multiple methods nicer. You will see it in the example down below.

And that's basically our Splitter! Now, we can try out some examples on how to use it.

### Example 1: Showing numbers, but squaring all odd numbers

In this very first example we want to operate over a list of numbers. For each even number, we just want to print this number out. But for every odd number, we want to square them before we print them out.

So first off, we want to split the list into even and odd numbers. After that, we can work on the lists as already told.

{% highlight java %}
public void workOnNumbers() {
    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

    Splitter.splitBy(numbers, num -> num%2 == 0)
            .workWithPassed(passed ->
                    passed.forEach(even -> System.out.println("" + even + " -> " + even)))
            .workWithNotPassed(notPassed ->
                    notPassed.map(odd -> odd * odd)
                            .forEach(odd -> System.out.println("" + Math.sqrt(odd) + " -> " + odd)
            ));
}
{% endhighlight %}

### Example 2: Sending all Winners a Confirmation and all Losers a Cancellation

Now, we have a List of `Candidates`. All of these people have a method called `hasWon()`, which gives back a boolean. We want to split the List into Winners and Losers. After that, we want to send all Winners a confirmation that they won, and all losers a cancellation that they lost. So let's do this!


{% highlight java %}
public void sendEmails(List<Candidates> candidates) {
    Splitter.splitBy(candidates, Candidates::hasWon)
            .workWithPassed(winners ->
                    winners.forEach(winner -> Email.send(winner.getEmail(), "You won!"))
    )
    .workWithNotPassed(losers ->
            losers.forEach(loser -> Email.send(loser.getEmail(), "You lost, sorry!"))
    );
}
{% endhighlight %}

## Update: On Using partitioningBy

After the post's out for a day now, a lot of you have given feedback. And I really appreciate it! You told me about ways to make the Splitter class better.

One of the discussed things is the use of `Stream.collect(Collectors.partitioningBy(Predicate<T> test))`. And I totally agree that it could be useful in our scenario.

It partitions the Stream depending on a test function. So for us, the map would look something like this: `{true: passed, false: notPassed}`. After that, we just take the two lists out of the map and go on.

So as `Philboyd_Studge` mentioned on Reddit, the new `splitBy` method could look something like this:

{% highlight java %}
public static <T> Splitter<T> splitBy(Collection<T> items,Predicate<T> test) {

        Map<Boolean, List<T>> map = items.stream()
                .collect(Collectors.partitioningBy(test));

        return new Splitter<T>(map.get(true), map.get(false));
}
{% endhighlight %}

And I have to admit that this takes out a lot of noise. It just looks nicer than the `splitBy` method above. So thank you for that!

### So what's the Splitter's Right to Exist?

The Splitter's purpose is to demonstrate how you can work with functions as objects. Its purpose is *not* to replace  some methods in the JDK.

It's a class for learning and playing around. If you want to tweak around, just do it. Please leave a comment on what you have learned or where you have optimized the class, so that others can learn from it too.

In addition, we learned about design patterns. Some of you even told me that they learned new design patterns in this blog post.

The patterns help to make the syntax nicer. When you split your collection in two parts, it's nicer to use the cascade pattern in the Splitter than to handle a Map.

## Conclusion
That's it for today!

We have learned how to create our first useful class with the help of Streams. We also optimized the run time of our program with it. Therefore, we have trained our knowledge about design patterns like the factory method and the cascade pattern.

Lastly, we have tried out our splitter with some examples.

Next time, we will talk about RxJava. It's a framework that uses the observer pattern for asynchronous tasks and builds upon functional programming.

Are there any things that your splitter should do too? Please let me know in the comments. I'd love to hear your feedback too!

Thanks for reading and have a nice day,

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
