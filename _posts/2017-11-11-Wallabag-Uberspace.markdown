# https://doc.wallabag.org/en/admin/installation/installation.html shared hosting
mkdir /var/www/virtual/$USER/wallabag
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package -C /var/www/virtual/$USER/wallabag --strip-components=1
cd /var/www/virtual/$USER/wallabag

#https://getcomposer.org/download/
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"

php bin/console wallabag:install --env=prod #Just install shut down server after this

uberspace-add-domain -d "*.$USER.$HOSTNAME" -w
uberspace-add-port -p both -f #Remember Port Number

php bin/console server:start 0.0.0.0:PORTNUMBER -e prod

Now you can access your wallabag server under $USER.$HOSTNAME:PORTNUM

Have a nice day,
Niklas




