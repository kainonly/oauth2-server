## Nginx Alpine

Quick and Easy Nginx Image

![MicroBadger Size](https://img.shields.io/microbadger/image-size/kainonly/nginx-alpine.svg?style=flat-square)
![MicroBadger Layers](https://img.shields.io/microbadger/layers/kainonly/nginx-alpine.svg?style=flat-square)
![Docker Pulls](https://img.shields.io/docker/pulls/kainonly/nginx-alpine.svg?style=flat-square)
![Docker Cloud Automated build](https://img.shields.io/docker/cloud/automated/kainonly/nginx-alpine.svg?style=flat-square)
![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/kainonly/nginx-alpine.svg?style=flat-square)

```shell
docker pull kainonly/nginx-alpine
```

Set docker-compose

```yaml
version: '3.7'
services:
  nginx:
    image: kainonly/nginx-alpine
    restart: always
    privileged: true
    sysctls:
      net.ipv4.ip_forward: 0
      net.ipv4.conf.default.rp_filter: 1
      net.ipv4.conf.default.accept_source_route: 0
      net.ipv4.tcp_syncookies: 1
      kernel.msgmnb: 65536
      kernel.msgmax: 65536
      kernel.shmmax: 68719476736
      kernel.shmall: 4294967296
      net.core.somaxconn: 40960
      net.ipv4.tcp_synack_retries: 1
      net.ipv4.tcp_syn_retries: 1
      net.ipv4.tcp_fin_timeout: 1
      net.ipv4.tcp_keepalive_time: 30
      net.ipv4.ip_local_port_range: 1024 65000
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/vhost:/etc/nginx/vhost
      - ./nginx/logs:/var/nginx
    ports:
      - 80:80
      - 443:443
```

volumes

- `/etc/nginx/nginx.conf` Main Config
- `/etc/nginx/vhost` Virtual domain name setting directory
- `/var/nginx` Nginx's log
- `/website` Virtual directory

Default Config

```conf
user nginx nginx;
worker_processes auto;

error_log /var/nginx/error.log info;
pid /var/run/nginx.pid;
lock_file /var/run/nginx.lock;

events {
    worker_connections 1024; 
    accept_mutex off;
}

http {
    include mime.types;
    server_names_hash_bucket_size 64;
    default_type application/octet-stream;
    client_max_body_size 16m;
    access_log off; 

    aio threads;
    sendfile on; 
    sendfile_max_chunk 256k;

    tcp_nopush on;
    tcp_nodelay on;

    gzip on; 
    gzip_disable "MSIE [1-6].(?!.*SV1)";
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_proxied any;
    gzip_min_length 1000;
    gzip_buffers 16 8k;
    gzip_comp_level 5;
    gzip_types text/plain text/css text/xml text/javascript application/json application/x-javascript application/xml application/xml+rss;

    log_format main $remote_addr - $remote_user [$time_local] "$request"  $status $body_bytes_sent "$http_referer"  "$http_user_agent" "$http_x_forwarded_for";

    proxy_connect_timeout 5;
    proxy_read_timeout 60;
    proxy_send_timeout 5;
    proxy_buffer_size 16k;
    proxy_buffers 4 64k;
    proxy_busy_buffers_size 128k;
    proxy_temp_file_write_size 128k;
    proxy_temp_path /var/nginx/proxy_temp;

    keepalive_timeout 5;

    server {
        listen 80 default;
        return 404;
    }

    include vhost/**/*.conf;
}
```
