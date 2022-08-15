.DEFAULT_GOAL := info

# In DigitalOcean you should use the VPC firewall, so all ufw directive will be unused
# https://docs.digitalocean.com/support/check-your-droplet-firewall-settings/

info:
	@echo "Makefile from cloudserver-config"
	@echo "================================"
	@grep '^.PHONY: .* #' Makefile | sed 's/\.PHONY: \(.*\) # \(.*\)/\1 \2/' | tr '>' '\t' | expand -t30

.PHONY: update_system # > Update Linux System
update_system:
	@echo "UPDATING SYSTEM..."
	apt update
	apt upgrade

.PHONY: install_apache # > Install Apache2
install_apache:
	@echo "INSTALLING APACHE2..."
	sudo apt install apache2
	a2enmod rewrite # install mod rewrite
	systemctl restart apache2

.PHONY: install_apache_ssl # > Install Apache2 SSL
install_apache_ssl:
	# https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-22-04
	apt install certbot python3-certbot-apache

.PHONY: generate_apache_ssl # > Generate Apache2 SSL Let's Encryt
generate_apache_ssl:
	# info: https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-22-04
	certbot --apache -v
	apache2ctl configtest
	systemctl reload apache2
	@echo 'Auto-Renewal status: '
	systemctl status certbot.timer
	@echo 'Auto-Renewal dry-run test'
	certbot renew --dry-run
	@echo "Now modify the conf files!"

.PHONY: install_php # > Install PHP 8.1
install_php:
	@echo "INSTALLING PHP..."
	apt install php8.1 libapache2-mod-php8.1 php8.1-cli php8.1-curl php8.1-dom php8.1-zip
	# install gd for matomo
	apt-get install php8.1-gd
	systemctl restart apache2

.PHONY: install_mysql # > Install MySQL database server
install_mysql:
	# https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04
	@echo "INSTALLING MYSQL..."
	apt install mysql-server
	@echo "Running mySQL. Paste this with your chosen password:\n"
	@echo "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'PASSWORD_ROOT';\n"
	mysql
	sudo mysql_secure_installation
	apt install php-mysql
	systemctl restart apache2

.PHONY: install_composer # > Install Composer
install_composer:
	@echo "INSTALLING COMPOSER..."
	apt install composer

.PHONY: install_npm # > Install NodeJS 18
install_npm:
	# https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04
	# we need node18 for ViteJs
	@echo "INSTALLING NODEJS 18..."
	# apt install npm
	cd ~
	curl -sL https://deb.nodesource.com/setup_18.x -o nodesource_setup.sh
	bash nodesource_setup.sh
	apt install nodejs
	node -v

.PHONY: make_links # > Create root symlinks
make_links:
	@echo "CREATING HOME LINKS FOR APACHE..."
	cd ~
	ln -s /var/log/apache2/ apachelog
	ln -s /etc/apache2/sites-available sites-available

.PHONY: verify_system # > Verify System Status
verify_system:
	systemctl status apache2
	systemctl is-active mysql

.PHONY: install_server # > INSTALL A NEW COMPLETE SERVER
install_server: install_apache install_php install_apache_ssl install_mysql install_composer install_npm
	ufw disable
	@echo "Setting TimeZone"
	timedatectl set-timezone Europe/Rome
	@echo "Apache Log dir: /var/log/apache2"
	@echo "Now modify /etc/apache2/sites-available/000-default.conf or add some config\n\n"
	@echo "remember a2ensite new sites!"

.PHONY: create_backup_structure # > Create a backup structure
create_backup_structure:
	mkdir /var/databaseDump
	chmod 777 /var/databaseDump

.PHONY: backup_database # > Backup all databases
backup_database:
	mysqldump --all-databases >> /var/databaseDump/database.sql