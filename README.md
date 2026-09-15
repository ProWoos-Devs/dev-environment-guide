# dev-environment-guide

## This guide explains how to create a local WordPress + WooCommerce development environment using:

- VirtualBox
- Ubuntu Server
- nginx
- MariaDB
- PHP-FPM
- WordPress
- WooCommerce

The environment is intended for local development and testing, not production use.

## 1. Prerequisites
Before starting, install:
- VirtualBox
- An Ubuntu Server ISO
### For this guide, the following versions are used:
- Ubuntu Server	26.04.1 LTS
- nginx	1.28.x
- PHP	8.5.x
- PHP-FPM	8.5.x
- MariaDB	11.8.x
- WordPress	6.x
- WooCommerce	Latest compatible version

Package versions may receive minor updates through Ubuntu's repositories. The important requirement is to use a PHP version supported by the WordPress and WooCommerce versions being installed.

## 2. Create the Ubuntu Server VM
Open VirtualBox and create a new virtual machine.

**Use the following settings:**

Type: Linux

Version: Ubuntu (64-bit)

Memory: 2048 MB

Processors: 1

Disk: 25 GB

Disk type: VDI

Attach the Ubuntu Server ISO to the VM's optical drive.

Follow the Ubuntu Installer:

Language: English

Username: (choose username)

OpenSSH ticked off

Start the VM.

## 3. Install nginx
After installing Ubuntu Server, install the required components on the server

Use the following commands:

```sudo apt update```

```sudo apt install nginx```



Then reload nginx and see if it's running:

```sudo systemctl reload nginx```

```sudo systemctl status nginx```



If it's not, use the following commands and if they fail reinstall nginx

```sudo systemctl enable nginx```

```sudo systemctl start nginx```



Then configure the firewall:

```sudo ufw enable```

```sudo ufw allow 'Nginx HTTP'```

## 4. Install MariaDB

Use the following commands:

```sudo apt update```

```sudo apt install mariadb-server mariadb-client galera-4```

```sudo mariadb-secure-installation```

Then confirm the installation:

```sudo systemctl status mariadb```

and if not running:

```sudo systemctl start mariadb```

Verify the installation by connecting as root

```mariadb -u root -p```

## 5. Install PHP-FPM

Use the following commands:

```sudo apt update```

```sudo apt install php-fpm -y```

Verify the installed version:

```php --version```

## 6. Port forwarding
For your device's browser to reach the VM. Go in VirtualBox Settings, Network, Adapter 1(NAT) and Port Forwarding, then simply add a new rule from host port 8080 to guest port 80. Open http://localhost:8080 on your browser.

## 7. PHP extensions
WordPress needs these PHP extensions, on the VM, use the following commands:

```sudo apt install php-mysql php-curl php-xml php-mbstring php-intl php-zip php-imagick```

```sudo systemctl restart php8.5-fpm```

## 8. The database
Use the following commands:

```sudo mariadb```

```CREATE DATABASE wordpress;```

```CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'choose-your-password';```


*(for the "choose-your-password" space, write your own password)*


```GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';```

```FLUSH PRIVILEGES;```

```EXIT;```

## 9. WordPress files
Download WordPress files on your VM:

```cd /tmp```

```wget https://wordpress.org/latest.tar.gz```

```tar -xzf latest.tar.gz```

```sudo mv wordpress /var/www/wordpress```

```sudo chown -R www-data:www-data /var/www/wordpress```


## 10. Nginx configuration
Replace *everything* in /etc/nginx/sites-available/default with this:

    server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/wordpress;
        index index.php index.html;
        server_name _;
    
        location / {
            try_files $uri $uri/ /index.php?$args;
        }
    
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php8.5-fpm.sock;
        }
    }

Then:

```sudo nginx -t```

```sudo systemctl reload nginx```

## 11. Wordpress on your browser
Open http://localhost:8080 again and follow the WordPress installer. Database name wordpress, username wpuser, your password, host localhost.

## 12. WooCommerce
 In the WordPress admin, Plugins > Add New Plugin, search WooCommerce, Install Now, Activate. For the sample data, go to Products > All Products > Import and upload sample_products.csv. That file is inside the WooCommerce zip you have to download, in the folder woocommerce/sample-data/.

 ## 13. Problems I ran into

 ### The browser cannot connect to localhost:8080

Make sure the VM is running and that VirtualBox port forwarding is configured correctly.

The rule should forward host port 8080 to guest port 80.

Also make sure nginx is running inside the VM:

```sudo systemctl status nginx```


### The PHP version or PHP-FPM socket does not match

Ubuntu's repositories may provide a different PHP version than the one used in this guide.

Check the installed version:

```php --version```

If, for example, PHP 8.4 is installed instead of PHP 8.5, the nginx configuration will need to use the corresponding socket, such as:

```/run/php/php8.4-fpm.sock```


### WordPress cannot connect to the database

Make sure MariaDB is running:

```sudo systemctl status mariadb```

Check that the database and user exist:

```sudo mariadb```

Make sure the WordPress installer uses the same database name, username, password, and host that were created earlier.

### nginx is not running

If ```sudo systemctl status nginx``` shows that nginx is not running, try:

```sudo systemctl start nginx```

```sudo systemctl enable nginx```

### WooCommerce sample products cannot be imported

Make sure you are using the sample_products.csv file from the WooCommerce package and that the CSV has not been modified.

In WordPress, go to Products > All Products > Import and select the CSV file.

If the import fails, check that the WooCommerce plugin is installed and activated before attempting the import.

### Where do I find the sample data file?

The official WooCommerce documentation says that **sample_products.csv** is included in the WooCommerce plugin ZIP

You can download the WooCommerce plugin ZIP directly from the official WooCommerce website:

https://woocommerce.com/download/

On that page, click Download WooCommerce.

Then locate the **sample_products.csv** file:

**Unzip the folder**, Go to: **woocommerce** > **sample-data**, and there is the **sample_products.csv** file

After that, import it like instructed before.
