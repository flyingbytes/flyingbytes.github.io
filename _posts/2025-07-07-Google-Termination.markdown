---
layout: post
title:  "How Google Shut Down My Android Play Store Account and Killed My Business"
date:   2025-07-07 15:00:00 +0100
categories: programming android kotlin context receiver contextreceiver kotlin17 kotlin1620
---


**TL;DR:** On May 29th, 2025, Google has shut down my Google Play Store developer account, thus blocking all of my apps and killing my business of publishing Android apps. They've given me no reason at all, and don't allow me to take any action to re-activate my account. It seems like moving my apps to a new account,  an idea that originated from the [Google support](#newA) itself, caused the termination.

*In case Google's reading this, my ticket numbers are 0-2181000039046 (new account) and 5-7732000037267  (old account).*

Update August 2025: Someone mentioned that I should have deleted my old account by hand instead of letting it automatically block. However, since then I've been able to re-enable the old account and properly delete it. However, the new account is still blocked and Google support does not want to enable it. So that's also not the issue I'm facing

Over the years, I've read in multiple blog posts like [this](https://medium.com/@raffaelet/my-seven-years-old-google-play-developer-account-has-been-terminated-ed321754cd51), [this](https://www.reddit.com/r/androiddev/comments/1clee9k/company_account_got_terminated_for_vague_reason/?share_id=jnvFTWsncgXF-CKVOCjCk&utm_content=1&utm_medium=android_app&utm_name=androidcss&utm_source=share&utm_term=1), or [that](https://www.reddit.com/r/androiddev/comments/1cle2uj/google_developer_account_terminated_after_getting/) about Google shutting down other people's developer accounts for arbitrary reasons. 


I've never thought this arbitrary termination could happen to me as well. However, on May 29th, 2025, exactly this worst-case scenario began to unfold.

In this post, I want to present my frustrating conversations with the Google support about this incident and how Google declines any help to reactivate my account. The goal of this post is to show how easy it is to loose your business if you lay it in the hands of a third party like Google. 

First, I'll tell the main points of the story. [Later](#longL), I will go into more detail and publish my conversations with the Google support.

## Long story short

In case you didn't know, Google insists to verify the legal name and address of every Play Store developer account since [May 2024](https://support.google.com/googleplay/android-developer/answer/14177239?hl=en). Furthermore, if you monetize apps, this highly confidential information will be easily accessible for every user of the Play Store, even if you are not logged in. Hence, scraping and storing legal information is pretty easy in this case.

I'm fine with Google checking my legal information. However, I'm not fine making this sensitive information publicly available. Hence, I've decided to make all of monetized apps free s.t. my information is kept private. Furthermore, the Google support told me that I'd have to create a new developer account and move all apps there. This is because my old account contained monetized apps in the past, hence I'd have to make my legal information publicly available even if I don't have monetized apps right now. The idea was to verify my new account and let my old and empty account suspend without any information provided. So far, so good.

On May 14th, 2025, my old account was suspended as this was the deadline for providing legal information. However, two weeks later, on May 29th, 2025, my new account with all apps was suspended as well.

[This Reddit comment](https://www.reddit.com/r/androiddev/comments/1clee9k/comment/l2t2rhe/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) points out that moving my apps probably caused the termination of the new account. Google seems to automatically suspend accounts that are "connected" to suspended accounts. As my old account was suspended (which is fine for me), my connected new account was suspended as well (which is not fine for me). As I've not update any apps in my developer accounts in the days prior and not changed any settings, this seems to be the logical conclusion.

However, opening an appeal and pointing out that this transfer of apps was suggested by Google itself didn't help to change Google's opinion on the matter.  As I can't be 100% sure that the transfer is the actual cause of the termination (as Google doesn't provide me the exact reason), there is nothing more I can't do. My account is still suspended to this day.

<a name="longL"></a>
## Long story long

Explanation: In this part of the story, I will copy-paste parts of the email conversation between the Google support and me. The most important part of every email is surrounded by "\*\*"-marks

I'm creating and publishing Android apps since 2015. In 2017, I've started monetizing them and making some money with them. Over the years, many of my apps gained traction, and some even got used in the context of university lectures. I've learned a lot by creating the business, working with the Google developer account and publishing a dozen apps.

At the beginning of 2025, I've had to make a decision. Either making it extremely easy for every Play Store user to see my legal address on every app page (even not logged in users like bots), or demonetizing all of my apps. 

```
Thanks for your reply.

I understand that you are having some concerns in regards to the new
requirements related to the information that will be shown publicly on the
Play Store.

As part of the new expanded developer verification requirements, developers
will be required to provide certain information when creating Play Console
which helps us to verify your identity and making sure that we provide safe
and wonderful app experiences for our users and a great opportunity for all
our developers to be successful.

   - For individual accounts, below are the details that will be shown
   publicly:
      - Developer name
      - Legal name
      - Country (taken from legal address)
      - Developer email address

**Kindly note that, if you decide to monetize your app on Google Play then
Google will display your full address.**
In addition, there are certain countries or regions that may require you to
complete additional requirements to comply with regulatory authorities. For
further information, you may refer to this *Help center article*
<https://support.google.com/googleplay/android-developer/answer/6223646?hl=en&ref_topic=12798386&sjid=17803400505414581127-AP>
.

To learn more about developer verifications, please visit *Play Console
Requirements*
<https://support.google.com/googleplay/android-developer/answer/10788890?hl=en>
and the *Play Console Help Center*
<https://support.google.com/googleplay/android-developer/answer/13628312>.

Thanks for working with us through this process and please don't hesitate
to reply to this email if you need further assistance. If we do not hear
from you, we will consider the matter closed.

```

It also had to be the correct legal address, something like a postbox doesn't work.

```

Thank you for your patience.

**Unfortunately you can't use a postbox address.**

Thanks for your understanding.

Regards,
...
Google Play Developer Support
```



The decision was pretty hard for me, but I've demonetized all of my apps to keep my address safe. For this to work, I've moved all of my apps to a new developer account of mine, as the Google support has told me.


<a name="newA"></a>
```
Thank you for your reply.

In order to provide transparency to our users, and avoid potential
regulatory issues, Google Play now requires all organization developers and
all monetizing individual developers to provide a verified address that is
displayed in full to Google Play users.
**If you do not intend to monetize on Google Play, you can create a new
account and transfer your app there, and we will assist you in closing your
current account. Individual non-monetizing developer accounts only have
their legal name and country of residence publicly displayed, and not the
full address.**

Thanks for working with us through this process and please don't hesitate
to reply to this email if you need further assistance. If we do not hear
from you, we will consider the matter closed.
```

This way, no payment information was linked to my (new) developer account anymore, hence I didn't have to publicly announce my real address everywhere. So far, so good. That's the way Google wanted it to be, and I didn't bother paying the extra 25 dollars for the second developer account. As mentioned, that's the recommended way from Google to keep my apps and hide my address.

However, after two weeks, Google blocked *both* of my developer accounts without any particular reason. As this transfer was the only thing I did in this time, I guess the suspension is connected to my account migration, as [this Reddit comment](https://www.reddit.com/r/androiddev/comments/1clee9k/comment/l2t2rhe/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) points out as well.

```
This is a notification that your Google Play Publisher account has been  
terminated.



Publishing Status
Status: Account Terminated

**Your Developer account remains terminated due to prior violations of the  
Developer Program Policies and Developer Distribution Agreement by this or  
associated, previously terminated Google Play Developer accounts.**

Please do not attempt to register a new developer account. Any new accounts  
will be closed, and your developer registration fee will not be refunded.

View policy status




Issue found: High Risk Behavior


We have identified a pattern of high risk or abuse associated with your  
Developer Account and are taking this action pursuant to the Policy  
Coverage policy.

About the Policy Coverage policy
We don't allow apps or app content that undermine user trust in the Google  
Play ecosystem. In assessing whether to include or remove apps from Google  
Play, we consider a number of factors including, but not limited to, a  
pattern of harmful behavior or high risk of abuse. We identify risk of  
abuse including, but not limited to, items such as app- and  
developer-specific complaints, news reporting, previous violation history,  
user feedback, and use of popular brands, characters, and other assets.


Submit an Appeal

Please read through this page to understand your policy violation. If you  
believe our decision may be incorrect, you can appeal. It may take up to 7  
days to receive a response, or longer in exceptional cases.
Please do not attempt to register a new developer account. We will not be  
restoring your account at this time.


Regards,
...
The Google Play Team
```

As the email suggests, I've submitted an appeal. I've asked the Google support team what happened. However, I've only got an answer that told me, again, that "strange" behaviour occurred within my account and thus it had to be terminated.


```
Thank you for your patience while I looked into your issue.

After reviewing your appeal, we're unable to reinstate your Google Play
Developer account.

We can confirm that we have identified a pattern of high risk or abuse
associated with your Developer Account and have taken this action pursuant
to Section 8.3 or 10.3 of Google Play’s Developer Distribution Agreement
<https://play.google.com/intl/en_us/about/developer-distribution-agreement.html>.
**As we previously explained, in order to prevent bad-faith developers from
gaming our systems and putting our users at risk in the process, we can’t
share the reasons we’ve concluded that your account is at high risk.**

Your Developer account remains terminated due to prior violations of the  
Developer
Program Policies <https://play.google.com/about/developer-content-policy/>
 and Developer Distribution Agreement
<https://play.google.com/intl/ALL_us/about/developer-distribution-agreement.html>  
by
this or associated, previously terminated Google Play Developer accounts.

Please do not attempt to register a new developer account. Any new accounts
will be closed, and your developer registration fee will not be refunded.

If you are located in the EU, you may have additional redress options.
Learn more about those potential options in the EU Out-of-Court Dispute
Resolution Help Center
<https://support.google.com/european-union-digital-services-act-redress-options/answer/13535501>.
Routing ID: ZLFS

Regards,
...
The Google Play Team
```

Asking again which actions I can do to re-activate my account, I've received the answer that Google can't tell me what's going on because I could start "gaming [Google's] system".

```
Thanks again for contacting the Google Play team.

**As we previously explained, in order to prevent bad-faith developers from
gaming our systems and putting our users at risk in the process, we can’t
share the reasons we’ve concluded that your account is at high risk.**

Your Developer account remains terminated due to prior violations of the  
Developer
Program Policies <https://play.google.com/about/developer-content-policy/>
 and Developer Distribution Agreement
<https://play.google.com/intl/ALL_us/about/developer-distribution-agreement.html>  
by
this or associated, previously terminated Google Play Developer accounts.

Please do not attempt to register a new developer account. Any new accounts
will be closed, and your developer registration fee will not be refunded.

Thank you for your understanding.

Regards,
...
The Google Play Team

Please visit the Google Play *Developer Policy Center*
<https://play.google.com/about/developer-content-policy/> and Google Play's  
*Academy
for App Success*
<https://www.google.com/url?q=https%3A%2F%2Fplayacademy.exceedlms.com%2Fstudent%2Fcatalog%3Futm_source%3Dshortlink%26utm_medium%3Dgco%26utm_campaign%3Dgeneric&sa=D&sntz=1&usg=AOvVaw3TU58z9sfaZs6auAAx9I4W>  
to
learn more about building policy compliant and high quality apps. You can
also visit the *Android Developers Blog*
<https://android-developers.googleblog.com/> for the latest Android and
Google Play news for app and game developers.
```

I find it especially rude that they end the conversation with a reference to the developer blog, with information that I'm forbidden to use anymore.
Asking again and again, I got the same "gaming the system"-mail three times without any new information whatsoever.

I've never received a strike in the Play Store before, no bad reviews or whatsoever. I have the rights to the content and names of all of my apps. Still, Google decided that my account had to be terminated.

To conclude the post, I just want you to make sure that dependencies on third parties can be good and bad. If you depend on a very powerful third party (like Google) for things to work, it can also happen that things go sideways.
