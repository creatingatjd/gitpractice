# gitpractice
How to create an Nginx Server Block
# Create the server block file in /etc/nginx/conf.d/ directory. The file name must end with .conf.
# sudo nano /etc/nginx/conf.d/example.com.conf
# Put the following text into the file. Replace the red texts with your own domain name. Don’t forget to create A records for your domain name in your DNS manager i.e CloudFlare


server {
  listen 80;
  listen [::]:80;
  server_name www.example.com example.com;
  root /usr/share/nginx/example.com/;
  index index.php index.html index.htm index.nginx-debian.html;

  error_log /var/log/nginx/wordpress.error;
  access_log /var/log/nginx/wordpress.access;

  location / {
    try_files $uri $uri/ /index.php;
  }

   location ~ ^/wp-json/ {
     rewrite ^/wp-json/(.*?)$ /?rest_route=/$1 last;
   }

  location ~* /wp-sitemap.*\.xml {
    try_files $uri $uri/ /index.php$is_args$args;
  }

  error_page 404 /404.html;
  error_page 500 502 503 504 /50x.html;

  client_max_body_size 20M;

  location = /50x.html {
    root /usr/share/nginx/html;
  }

  location ~ \.php$ {
    fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
    include snippets/fastcgi-php.conf;
    fastcgi_buffers 1024 4k;
    fastcgi_buffer_size 128k;
  }

  #enable gzip compression
  gzip on;
  gzip_vary on;
  gzip_min_length 1000;
  gzip_comp_level 5;
  gzip_types application/json text/css application/x-javascript application/javascript image/svg+xml;
  gzip_proxied any;

  # A long browser cache lifetime can speed up repeat visits to your page
  location ~* \.(jpg|jpeg|gif|png|webp|svg|woff|woff2|ttf|css|js|ico|xml)$ {
       access_log        off;
       log_not_found     off;
       expires           360d;
  }

  # disable access to hidden files
  location ~ /\.ht {
      access_log off;
      log_not_found off;
      deny all;
  }
}
