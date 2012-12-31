Apache+PHP build pack
========================

This is a build pack bundling PHP and Apache for Heroku apps.

Configuration
-------------

The config files are bundled with the build pack itself:

* conf/httpd.conf
* conf/php.ini


Pre-compiling binaries
----------------------

    # apache
    mkdir /app
    wget http://apache.cyberuse.com//httpd/httpd-2.2.19.tar.gz
    tar xvzf httpd-2.2.19.tar.gz
    cd httpd-2.2.19
    ./configure --prefix=/app/apache --enable-rewrite
    make
    make install
    cd ..
    
    # php
    wget http://us2.php.net/get/php-5.3.6.tar.gz/from/us.php.net/mirror 
    mv mirror php.tar.gz
    tar xzvf php.tar.gz
    cd php-5.3.6/
    ./configure --prefix=/app/php --with-apxs2=/app/apache/bin/apxs --with-mysql --with-pdo-mysql --with-pgsql --with-pdo-pgsql --with-iconv --with-gd --with-curl=/usr/lib --with-config-file-path=/app/php --enable-soap=shared --with-openssl
    make
    make install
    cd ..
    
    # php extensions
    mkdir /app/php/ext
    cp /usr/lib/libmysqlclient.so.15 /app/php/ext/
    
    # pear
    apt-get install php5-dev php-pear
    pear config-set php_dir /app/php
    pecl install apc
    mkdir /app/php/include/php/ext/apc
    cp /usr/lib/php5/20060613/apc.so /app/php/ext/
    cp /usr/include/php5/ext/apc/apc_serializer.h /app/php/include/php/ext/apc/
    
    
    # package
    cd /app
    echo '2.2.19' > apache/VERSION
    tar -zcvf apache.tar.gz apache
    echo '5.3.6' > php/VERSION
    tar -zcvf php.tar.gz php


Hacking
-------

To change this buildpack, fork it on Github. Push up changes to your fork, then create a test app with --buildpack <your-github-url> and push to it.


Meta
----

Created by Pedro Belo.
Many thanks to Keith Rarick for the help with assorted Unix topics :)


Updated by howanghk
-------------------

PHP 5.4.10 compiled with:

    curl -O http://us3.php.net/distributions/php-5.4.10.tar.bz2
    tar jxvf php-5.4.10.tar.bz2
    cd php-5.4.10
    ./configure --prefix=/app/php --with-apxs2=/app/apache/bin/apxs --with-mysql --with-pdo-mysql --with-pgsql --with-pdo-pgsql --with-iconv --with-gd --with-curl=/usr/lib --with-config-file-path=/app/php --enable-soap=shared --with-openssl --with-mcrypt=/app/local --enable-bcmath --enable-mbstring
    make
    make install

libmcrypt 2.5.8 compiled with:

    curl -L -O http://downloads.sourceforge.net/project/mcrypt/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.bz2
    tar jxvf libmcrypt-2.5.8.tar.bz2
    cd libmcrypt-2.5.8
    ./configure --prefix=/app/local --disable-rpath --disable-posix-threads --enable-dynamic-loading
    make
    make install

To use this buildpack for a new app:

    heroku create --buildpack https://github.com/howanghk/heroku-buildpack-php

To use this buildpack for an existing app:

    heroku config:add BUILDPACK_URL=https://github.com/howanghk/heroku-buildpack-php

Happy PHP'ing!
