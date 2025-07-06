ubuntu image 22.04 LTS



```
sudo apt update && sudo apt upgrade -y


# nginx
sudo apt install nginx -y
sudo rm -rf /etc/nginx/sites-available/default
sudo rm -rf /etc/nginx/sites-enabled/default
sudo touch /etc/nginx/sites-available/nextcloud
sudo ln -s /etc/nginx/sites-available/nextcloud /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# tailscale
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
tailscale ip -4
## open 100 ip for tailscale
sudo ufw allow from 100.0.0.0/8 to any port 80 proto tcp

# php
sudo apt install php-fpm php-mysql php-xml php-zip php-gd php-curl php-mbstring php-intl php-bcmath php-gmp -y

# mariadb
sudo apt install mariadb-server -y
sudo mysql_secure_installation
sudo mysql -u root -p
CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# nextcloud
cd /var/www/
sudo wget https://download.nextcloud.com/server/releases/latest.zip
sudo apt install unzip -y
sudo unzip latest.zip
sudo chown -R www-data:www-data nextcloud
sudo chmod -R 755 nextcloud

# firewall
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable

# create data space for nc
sudo mkdir /mnt/nextcloud_data
sudo chown -R www-data:www-data /mnt/nextcloud_data
sudo chmod 750 /mnt/nextcloud_data

```



```nginx
server {
    listen 80;
    server_name _;

    root /var/www/nextcloud;

    client_max_body_size 512M;
    fastcgi_buffers 64 4K;

    add_header Referrer-Policy "no-referrer" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;

    index index.php index.html;

    location / {
        rewrite ^ /index.php$request_uri;
    }

    location ~ \.php(?:$|/) {
        rewrite ^/(?!index|remote|public|cron|core/ajax/update|status|ocs/v[12]|updater/.+|ocs-provider/.+|.+/richdocumentscode(_arm64)?/proxy) /index.php$request_uri;

        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        set $path_info $fastcgi_path_info;

        try_files $fastcgi_script_name =404;

        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $path_info;
        fastcgi_param HTTPS off;

        fastcgi_param modHeadersAvailable true;
        fastcgi_param front_controller_active true;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;

        fastcgi_intercept_errors on;
        fastcgi_request_buffering off;
    }

    location ~ \.(?:css|js|mjs|svg|gif|ico|jpg|png|webp|wasm|woff2?|ttf|otf)$ {
        try_files $uri /index.php$request_uri;
        access_log off;
        add_header Cache-Control "public, max-age=15778463, immutable";
    }

    location ~ ^/(?:\.|autotest|occ|issue|indie|db_|console) { return 404; }
    location ~ ^/(?:build|tests|config|lib|3rdparty|templates|data)(?:$|/) { return 404; }

    location ^~ /.well-known {
        location = /.well-known/carddav { return 301 /remote.php/dav/; }
        location = /.well-known/caldav  { return 301 /remote.php/dav/; }
        location /.well-known/acme-challenge { try_files $uri =404; }
        location /.well-known/pki-validation { try_files $uri =404; }
        return 301 /index.php$request_uri;
    }

    location ~ /\.ht { deny all; }
}

```

