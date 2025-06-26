###  WordPress setup on Ubuntu 


---

#### 📋 Prerequisites

- Vagrant installed on host machine
- VirtualBox installed
- Ubuntu 22.04 or similar base box
- Internet connection for package installation

---

#### If you want to setup a wordpress website on ubuntu server these are the steps to follow

---

###  1. Sstup an Ubuntu machine using vagrant

- Name your Project

```bash
mkdir wordpress
cd wordpress
vagrant init ubuntu/focal64
```

- Edit Vagrantfile to customize your ip and create the folder


```
config.vm.network "private_network", ip: "192.168.56.100"
config.vm.synced_folder ".", "/srv/www/wordpress"
```

-  Run the below command to start the VM

```bash
vagrant up
vagrant ssh
```
---


###  2. Set Hostname (Optional)

```bash
sudo hostnamectl set-hostname wordpress
```


---


###  3. Install Apache, MySQL, PHP (LAMP)

-  Update Package Index

```bash
sudo apt update
sudo apt upgrade -y
```

-  Install Apache

```bash
sudo apt install apache2 -y
```

- Install MySQL


```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

- Create MySQL Database & User

```bash
sudo mysql
```

- In the MySQL shell:

```sql
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'babyGurl38';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```


- Install PHP

```bash
sudo apt install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip unzip -y
```

---

### 4. Download and Configure WordPress

- Download WordPress

```bash
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
```

- Move WordPress Files

```bash
sudo mkdir -p /srv/www/wordpress
sudo cp -a /tmp/wordpress/. /srv/www/wordpress
```

- Set Permissions

```bash
sudo chown -R www-data:www-data /srv/www/wordpress
sudo find /srv/www/wordpress -type d -exec chmod 750 {} \;
sudo find /srv/www/wordpress -type f -exec chmod 640 {} \;
```

---

### 5. Configure WordPress Database Connection

- Create wp-config.php

```bash
cd /srv/www/wordpress
sudo -u www-data cp wp-config-sample.php wp-config.php
sudo -u www-data vim wp-config.php
```

- Edit These Lines:

```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', '08133211856' );
define( 'DB_HOST', 'localhost' );
```

-  Add Salt Keys

Copy and paste the generated keys to replace:

```php
define( 'AUTH_KEY',         'put your unique phrase here' );
...
define( 'NONCE_SALT',       'put your unique phrase here' );
```

---

###  6. Configure Apache to Serve WordPress

- Create Apache Virtual Host

```bash
sudo vim /etc/apache2/sites-available/wordpress.conf
```

Paste:

```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /srv/www/wordpress
    ServerName wordpress.local

    <Directory /srv/www/wordpress>
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

- Enable Config and Restart Apache

```bash
sudo a2ensite wordpress
sudo a2enmod rewrite
sudo systemctl reload apache2
```

---


###  7. Access WordPress in Browser

```
192.168.56.100 or your configured ip
```
---

You should now see the WordPress installation screen as shown on the screenshot above.

---


