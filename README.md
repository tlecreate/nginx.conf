# Example nginx config for any use cases

- [reverse proxy (With load balance)](sites-template/default.reverse-proxy.https.conf)
- [reverse proxy + proxy_cache (With load balance)](sites-template/default.reverse-proxy.cache.https.conf)
- [php_fastcgi + fastcgi_cache](sites-template/default.php_fastcgi.cache.https.conf)
- [Static html + fastcgi_cache](sites-template/default.html_fastcgi.cache.https.conf.conf)

## How to create new site

- Make directory for use path

```sh
export DOMAIN=youdomain.com

# letsencrypt
    mkdir -p /var/www/_letsencrypt

# proxy, fastchi cache
    mkdir -p /var/cache/nginx/

# path of ssl certificate.
    mkdir -p /etc/ssl/$DOMAIN

# path of scripts for php or wordpress
    mkdir -p /var/www/$DOMAIN/htdocs

# path of scripts for static html
    mkdir -p /var/www/$DOMAIN/public

# phpmyadmin
    mkdir -p /var/www/phpmyadmin/htdocs
```

- copy needed template and replace example to your domain

```sh
# don't forget to set DOMAIN from above example
# export DOMAIN=youdomain.com

# copy
    cp ./sites-templates/default.php_fastcgi.cache.https.conf ./sites-available/$DOMAIN.conf

# replace example.com to yourdomain.com
    sed -i -r "s/example.com/$DOMAIN/g" ./sites-available/$DOMAIN.conf
```

## Free SSL

- [let's encrypt with certbot]('https://certbot.eff.org/docs/')

```sh
# This example inspire from https://nginxconfig.io

# If you want to use certbot please change to ssl path /etc/ssl to /etc/letsencrypt/live
    sed -i -r "s/\/etc\/ssl/\/etc\/letsencrypt\/live/g" ./sites-available/$DOMAIN.conf

# Comment out SSL related directives in configuration:
    sed -i -r 's/(listen .*443)/\1;#/g; s/(ssl_(certificate|certificate_key|trusted_certificate) )/#;#\1/g' /etc/nginx/sites-available/$DOMAIN.conf

# Reload NGINX:
    sudo nginx -t && sudo systemctl reload nginx

# Obtain certificate:
    certbot certonly --webroot -d $DOMAIN -d www.$DOMAIN --email info@$DOMAIN -w /var/www/_letsencrypt -n --agree-tos --force-renewal

# Uncomment SSL related directives in configuration:
    sed -i -r 's/#?;#//g' /etc/nginx/sites-available/$DOMAIN.conf

# Configure Certbot to reload NGINX after success renew:
    echo -e '#!/bin/bash\nnginx -t && systemctl reload nginx' | sudo tee /etc/letsencrypt/renewal-hooks/post/nginx-reload.sh
    sudo chmod a+x /etc/letsencrypt/renewal-hooks/post/nginx-reload.sh

# Reload NGINX:
    sudo nginx -t && sudo systemctl reload nginx
```

## How to use phpmyadmin

bind port 8080 from remote host to 8080 of local

```sh
ssh -N -L 8080:127.0.0.1:8080 user@host
```

or run with private.pem key

```sh
ssh -i "private.pem" -N -L 8080:127.0.0.1:8080 user@host
```

then open phpmyadmin with the url => [http://localhost:8080](//localhost:8080)

## TLDR

- this repo was inspire from ['nginxconfig.io'](https://nginxconfig.io) almost config coverage for my use cases
- if anyone want more example please open the issue
- I very appreciate if anyone can sent the pull request for improve any my mistake or improve something more than current example or more example for another use cases :D
