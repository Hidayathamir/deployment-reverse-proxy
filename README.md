# deployment-reverse-proxy

## pre

install docker

## step

run docker image enriquecatala/fastapi-helloworld
```
docker run -d -p 5000:5000 enriquecatala/fastapi-helloworld
```

nginx http

```
cd /etc/nginx/sites-available
touch go-quote 
server {
    listen 443 ssl;
    server_name hidayathamir.my.id;
    ssl_certificate /etc/letsencrypt/live/hidayathamir.my.id/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/hidayathamir.my.id/privkey.pem;
    return 301 https://www.hidayathamir.my.id;
}

server {
    listen 443 ssl;
    server_name www.hidayathamir.my.id;
    ssl_certificate /etc/letsencrypt/live/www.hidayathamir.my.id/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/www.hidayathamir.my.id/privkey.pem;
    location / {
        proxy_pass http://localhost:8080;
    }
}

server {
    listen 80;
    server_name hidayathamir.my.id;
    return 301 https://www.hidayathamir.my.id;
}

server {
    listen 80;
    server_name www.hidayathamir.my.id;
    return 301 https://www.hidayathamir.my.id;
}
```

go to ip
domain to ip
make it www
go to domain with http
https
go to domain with https
