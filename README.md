# NGINX Demo

## Install NGINX
1. Download NGINX signing key & add to key repository
```sh
wget http://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
```

2. Add NGINX distribution URLs to sources
```sh
sudo vim /etc/apt/sources.list
```
```
deb http://nginx.org/packages/ubuntu xenial nginx
deb-src http://nginx.org/packages/ubuntu xenial nginx
```

3. Update and install NGINX
```sh
sudo apt-get update
sudo apt-get install nginx
```

## Configure NGINX
### Simple Server & Static Files
- Location block
- Static assets/images block

```
server {
  listen 80;
  server_name localhost;

  location / {
    root /home/ubuntu/simple-server;
  }

  location /images {
    alias /home/ubuntu/images;
  }
}
```
_reference: `1.simple-server.conf`_

### Load Balanced Server
- Server config for each application instance
- Main server w/ upstream server group

```
# configure app servers
server {
  listen 8081;
  server_name localhost;
  root /home/ubuntu/load-balanced/app1;
}

server {
  listen 8082;
  server_name localhost;
  root /home/ubuntu/load-balanced/app2;
}

# define upstream server group
upstream appServers {
  server 127.0.0.1:8081;
  server 127.0.0.1:8082;
}

# configure main server and location blocks
server {
  listen 80;
  server_name localhost;
  root /home/ubuntu/load-balanced;

  location / {
    proxy_pass http://appServers;
  }

  location /images {
    alias /home/ubuntu/images;
  }
}
```
_reference: `2.load-balanced-server.conf`_
