# deployment-reverse-proxy

## pre

install docker

## step

run docker image enriquecatala/fastapi-helloworld
```
docker run -d -p 5000:5000 enriquecatala/fastapi-helloworld
```

## 3. Reverse proxy our local to internet using nginx

### 3.1 Enable firewall, install nginx and allow firewall nginx

```
sudo apt install nginx -y
sudo ufw allow OpenSSH
sudo ufw allow 22
sudo ufw allow "Nginx Full"
sudo ufw enable
```

### 3.2 Setup nginx to map port 80 to port 8080

```
sudo nano /etc/nginx/sites-available/bazr-backend
```

```
server {
   listen 80;
   listen [::]:80;

   location / {
     proxy_pass http://localhost:8080;
   }
}
```

basically we say to nginx that if there’s any request on port 80 please give it to localhost:8080

### 3.3 Remove nginx default sites enabled

```
sudo rm /etc/nginx/sites-enabled/default
```

### 3.4 Add link from our nginx setup to nginx sites enabled

```
sudo ln -s /etc/nginx/sites-available/bazr-backend /etc/nginx/sites-enabled/bazr-backend
```

### 3.5 Reload nginx

```
sudo nginx -s reload
```

now our sites is running, try to access http://157.230.240.209/docs



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
