Heroku Buildpack (Nginx + Google Pagespeed)
----------------------------------
Buildpack Stack:
* Nginx 1.7.10
* Google Pagespeed 1.8.31.4

Heroku Requirement:
* Cedar-14

Compile and Package Nginx
----------------------------------
This script should be excuted via the terminal ```heroku run bash --app=app-name```.
```
# set starting point
cd ~

# vendor nginx
curl -L http://nginx.org/download/nginx-1.7.10.tar.gz | tar xzv
cd ~/nginx-*
curl -L http://garr.dl.sourceforge.net/project/pcre/pcre/8.36/pcre-8.36.tar.bz2 | tar xvj
curl -L http://drocventures.s3.amazonaws.com/heroku/buildpacks/pagespeed-1.9.32.3.tar.gz | tar xzv
cd ngx_pagespeed-release-*
curl -L https://dl.google.com/dl/page-speed/psol/1.9.32.3.tar.gz | tar xzv
cd ~/nginx-*
./configure \
    --prefix=/app/vendor/nginx \
    --with-pcre=pcre-8.36 \
    --with-http_ssl_module \
    --with-http_gzip_static_module \
    --with-http_stub_status_module \
    --with-http_realip_module \
    --add-module=ngx_pagespeed-release-1.9.32.3-beta
make install

# package nginx
cd /app/vendor/nginx && tar cvzf /tmp/nginx-1.7.10.tar.gz .
```
Once the package is created, execute the following commands to download it from the dyno.
```
cd /tmp/
curl https://raw.githubusercontent.com/scottmotte/srvdir-binary/master/srvdir.tar.gz -O
tar -zxvf srvdir.tar.gz
./srvdir
```

Note
----
To make our lives easier when we built this package, we created a fresh dyno ```heroku create``` and then terminal into it via ```heroku run bash -a app-name```.
