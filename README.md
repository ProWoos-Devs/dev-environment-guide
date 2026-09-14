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


Start the VM.

## 3. Install nginx
After installing Ubuntu Server, install the required components on the server

Use the following commands:

- **sudo apt update**

- **sudo apt install nginx**



Then reload nginx and see if it's running:

- **sudo systemctl reload nginx**

- **sudo systemctl status nginx**



If it's not, use the following commands and if they fail reinstall nginx

- **sudo systemctl enable nginx**

- **sudo systemctl start nginx**



Then configure the firewall:

- **sudo ufw enable**

- **sudo ufw allow 'Nginx HTTP'**

## 4. Install MariaDB

Use the following commands:

- **sudo apt update**
- **sudo apt install mariadb-server mariadb-client galera-4**
- **sudo mariadb-secure-installation**

Then confirm the installation:

- **sudo systemctl status mariadb**

and if not running:

- **sudo systemctl start mariadb**

Verify the installation by connecting as root

- **mariadb -u root -p**

## 5. Install PHP-FPM

Use the following commands:

- **sudo apt update**
- **sudo apt install php -y**

Verify the installed version:

- **php --version**

## 6. Porting forward
For your device's browser to reach the VM. Go in VirtualBox Settings, Network, Adapter 1(NAT) and Port Forwarding, then simply add a new rule from host port 8080 to guest port 80. Open http://localhost:8080 on your browser.

## 7. PHP extensions
Wordpress needs these PHP extensions, on the VM, use the following commands:

- **sudo apt install php-mysql php-curl php-xml php-mbstring php-intl php-zip php-imagick**
- **sudo systemctl restart php8.5-fpm**

## 8. The database
Use the following commands:

- **sudo mariadb**
- **CREATE DATABASE wordpress;**
- **CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'choose-your-password';**

*(for the "choose-your-password" space, write your own password)*

- **GRANT ALL PRIVILEGES ON wordpress.* TO 'wpsuer'@'localhost';**
- **FLUSH PRIVILEGES;**
- **EXIT;**
