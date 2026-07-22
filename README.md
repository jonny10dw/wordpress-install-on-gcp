# WordPress Installation on Google Cloud Platform (GCP)

> A quick reference for deploying a WordPress server on a Google Compute Engine VM using Bash commands.

---

## 1. Docker compose file
> Template to be used to spin up mariadb in docker container

---

## 2. Wordpress NGINX config
> Template for using NGINX with Wordpress

---

## 3. Commands to run step by step

```bash
# upgrade all server packages
$ sudo apt update && sudo apt upgrade -y

# install ufw, nginx, php, php-fpm, zip, unzip, nano, certbot, certbot nginx plugin
$ sudo apt install -y ufw nginx php php-fpm php-mysqli zip unzip nano certbot python3-certbot-nginx

# disable apache2, we are using nginx
$ sudo systemctl disable apache2

# reboot here to make sure all the updates are running
$ sudo reboot

# make sure nginx is running
$ sudo systemctl status nginx

# allow port 80 via firewall
$ sudo ufw allow 80/tcp

# allow port 80 via firewall
$ sudo  ufw allow 443/tcp

# allow port 80 via firewall
$ sudo ufw allow 22/tcp

# enable the firewall with rules set above
$ sudo ufw enable

# check the status of the firewall
$ sudo ufw status

# run through docker install procedure
# follow the steps located here
# https://docs.docker.com/engine/install/ubuntu/

# download wordpress latest version
$ curl -L https://wordpress.org/latest.zip -o latest.zip

# unzip the package
$ unzip latest.zip

# move wordpress to /var/www/wordpress
$ sudo mv wordpress /var/www/wordpress

# modify the docker compose file with updated passwords
$ nano docker-compose.yaml

# start the mariadb
$ sudo docker compose up -d

# move to the wordpress files location
$ cd /var/www/wordpress/

# copy the config file
$ sudo cp wp-config-sample.php wp-config.php

# get the salts from wordpress
$ curl https://api.wordpress.org/secret-key/1.1/salt/

# modify the config file
# update the lines with
# define( 'DB_NAME', 'wordpress' );
# define( 'DB_USER', 'wordpress' );
# define( 'DB_PASSWORD', 'PutYourPasswordHere :)' );
# define( 'DB_HOST', '127.0.0.1:3306' );
# and add the salts from the command above in their section
$ sudo nano wp-config.php

# make nginx user www-data the owner of the files
$ sudo chown -R www-data:www-data /var/www/wordpress

# update the wordpress directories to 755
$ sudo find /var/www/wordpress -type d -exec chmod 755 {} \;

# update the wordpress files to 644
$ sudo find /var/www/wordpress -type f -exec chmod 644 {} \;

# setup wordpress on nginx
# open file and modify the domain name
$ sudo nano /etc/nginx/sites-available/wordpress.conf

# link the wordpress config to nginx available sites to sites enabled
$ sudo ln -s /etc/nginx/sites-available/wordpress.conf /etc/nginx/sites-enabled/wordpress

# check that the nginx config is valid
$ sudo nginx -t

# restart nginx service
$ sudo systemctl restart nginx

# use certbot to get a cert
$ sudo certbot --nginx -d enter_your_domain_here
```
