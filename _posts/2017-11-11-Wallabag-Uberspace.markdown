---
layout: post
title:  "How to Set Up Wallabag on Uberspace"
date:   2017-11-11 13:00:00 +0100
categories: Wallabag Uberspace
---
Today, I want to show you how to set up your own Wallabag instance on an Uberspace server.

## Set Up Composer

First of all, we will need Composer for using Wallabag. You can find the guide I used for this [here][Composer]:

```
mkdir /var/www/virtual/$USER/wallabag
cd /var/www/virtual/$USER/wallabag
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

## Set Up Wallabag

We will use [this][Wallabag] guide for installing Wallabag on our server. For that, let's concentrate on the shared hosting part here:

```
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package -C /var/www/virtual/$USER/wallabag --strip-components=1
cd /var/www/virtual/$USER/wallabag
php bin/console wallabag:install --env=prod #Remember the username + password
```

You can shut down this Wallabag instance, because it uses port 8000, which Uberspace doesn't open for the outside world.

## Adapt Wallabag for Uberspace

Now, we're going to set up our domain and after that, we want to start Wallabag, but with a different port:

```
uberspace-add-domain -d "*.$USER.$HOSTNAME" -w
uberspace-add-port -p both -f #Remember the number of the port.
php bin/console server:start 0.0.0.0:PORTNUMBER -e prod
```

Finally, you have access to your Wallabag server from anywhere under `$USER.$HOSTNAME:PORTNUMBER`. You can login with the username and password you used when you set up Wallabag (default is Wallabag/Wallabag).

Thanks for reading and have a nice day, please leave some feedback too!

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

[Wallabag]: https://doc.wallabag.org/en/admin/installation/installation.html
[Composer]: https://getcomposer.org/download/
