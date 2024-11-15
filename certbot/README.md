## Let's Encrypt Using NGINX and Certbot

[Blog](https://mindsers.blog/post/https-using-nginx-certbot-docker)

```docker
# get certificates
$ docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d example.org

# renew
$ docker compose run --rm certbot renew
```

nginx config

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name example.org www.example.org;
    server_tokens off;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://example.org$request_uri;
    }
}

server {
    listen 443 default_server ssl http2;
    listen [::]:443 ssl http2;

    server_name example.org;

    ssl_certificate /etc/nginx/ssl/live/example.org/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/example.org/privkey.pem;
    
    location / {
    	# ...
    }
}
```