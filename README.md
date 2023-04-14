# deployment-reverse-proxy

If you like video format, [here](https://youtube.com/playlist?list=PLGiTvBmFA11_JEyYAui6tTv65HsS8HWvq) is the youtube video.

## 1. Pre-requisites

install docker

## 2. Step-by-step Deployment

### 2.1 Run Docker image

```
sudo docker run -d -p 5000:5000 enriquecatala/fastapi-helloworld
```

make sure it's run properly by test it with

```
curl localhost:5000/docs
```

### 2.2 Install Nginx and enable firewall

```
sudo apt update
sudo apt install nginx -y
sudo ufw app list
sudo ufw status
sudo ufw allow OpenSSH
sudo ufw allow "Nginx Full"
sudo ufw enable
sudo ufw status
```

### 2.3 Reverse Proxy using Nginx

#### 2.3.1 Configure Nginx to map port 80 to 5000

Create a new Nginx configuration file:

```
sudo nano /etc/nginx/sites-available/reverse-proxy
```

Paste the following configuration into the file:

```
server {
   listen 80;
   location / {
     proxy_pass http://localhost:5000;
   }
}
```

basically we say to nginx that if there’s any request on port 80 please give it to localhost:5000

#### 2.3.2 Remove Nginx default site

```
sudo rm /etc/nginx/sites-enabled/default
```

#### 2.3.3 Enable your Nginx configuration

Create a symbolic link from the configuration file to the sites-enabled directory:

```
sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/reverse-proxy
```

Check the syntax of your configuration:

```
sudo nginx -t
```

#### 2.3.4 Reload Nginx

```
sudo nginx -s reload
```

now our sites is running, try to access http://your.ip.your.ip/

### 2.4 Register a domain and point its DNS to your server's IP address

#### 2.4.1 Configure the DNS settings for your domain

Once you have registered a domain name, you need to configure its DNS settings to point to your server's IP address. This is done by adding an "A" record to your domain's DNS settings. The "A" record maps your domain name to the IP address of your server. I recommend you to watch [this](https://youtu.be/QcNBLSSn8Vg) youtube video about "How to Point a Domain Name to an IP Address (DNS A record example)"

#### 2.4.2 Wait for DNS propagation

After you have updated your domain's DNS settings, it can take some time for the changes to propagate throughout the internet

#### 2.4.3 Update Nginx configuration

```
sudo nano /etc/nginx/sites-available/reverse-proxy
```

```
server {
    listen 80;
    server_name yourdomain.com;
    return 301 http://www.yourcomain.com;
}

server {
    listen 80;
    server_name www.yourdomain.com;
    location / {
        proxy_pass http://localhost:5000;
    }
}
```

#### 2.4.4 Reload Nginx

```
sudo nginx -s reload
```

now our sites is running, try to access http://www.yourdomain.com/

### 2.5 Configure HTTPS using Let's Encrypt

#### 2.5.1 Install Certbot

```
sudo apt update
sudo apt install snapd
sudo snap install --classic certbot
```

Create a symbolic link for Certbot:

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

#### 2.5.2 Generate SSL certificate using Certbot

```
sudo certbot certonly --nginx
```

#### 2.5.3 Update Nginx configuration

```
sudo nano /etc/nginx/sites-available/reverse-proxy
```

```
server {
    listen 443 ssl;
    server_name yourdomain.com;
    ssl_certificate /etc/letsencrypt/live/www.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/www.yourdomain.com/privkey.pem;
    return 301 https://www.yourdomain.com;
}

server {
    listen 443 ssl;
    server_name www.yourdomain.com;
    ssl_certificate /etc/letsencrypt/live/www.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/www.yourdomain.com/privkey.pem;
    location / {
        proxy_pass http://localhost:5000;
    }
}

server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://www.yourdomain.com;
}

server {
    listen 80;
    server_name www.yourdomain.com;
    return 301 https://www.yourdomain.com;
}
```

#### 2.5.4 Reload Nginx

```
sudo nginx -s reload
```

now our sites is running, try to access https://www.yourdomain.com/
