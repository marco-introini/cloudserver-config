update_system:
	@echo "UPDATING SYSTEM..."
	apt update
	apt upgrade

install_apache:
	@echo "INSTALLING APACHE2..."
	sudo apt install apache2
	# Install firewall if not already done
	# apt install ufw -y
	# sudo systemctl enable ufw --now
	ufw allow 80/tcp
	ufw allow 443/tcp
	ufw allow 22/tcp
	ufw deny 3306/tcp
	ufw enable
	ufw status verbose
	a2enmod rewrite # install mod rewrite
	systemctl restart apache2

install_apache_ssl:
	apt install certbot python3-certbot-apache
	ufw allow 'Apache Full'

generate_apache_ssl:
	# info: https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-22-04
	ufw allow 'Apache Full'
	certbot --apache -v
	apache2ctl configtest
	systemctl reload apache2
	@echo 'Auto-Renewal status: '
	systemctl status certbot.timer
	@echo 'Auto-Renewal dry-run test'
	certbot renew --dry-run
	@echo "Now modify the conf files!"

install_php:
	@echo "INSTALLING PHP..."
	apt install php8.1 libapache2-mod-php8.1 php8.1-cli php8.1-curl php8.1-dom php8.1-zip
	# install gd for matomo
	apt-get install php8.1-gd
	systemctl restart apache2

install_mysql:
	@echo "INSTALLING MYSQL..."
	apt install mysql-server
	@echo "Running mySQL. Paste this with your chosen password:\n"
	@echo "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'PASSWORD_ROOT';\n"
	mysql
	sudo mysql_secure_installation
	apt install php-mysql
	systemctl restart apache2

install_composer:
	@echo "INSTALLING COMPOSER..."
	apt install composer

install_npm:
	@echo "INSTALLING NODEJS..."
	apt install npm

make_links:
	@echo "CREATING HOME LINKS FOR APACHE..."
	cd
	ln -s /var/log/apache2/ apachelog
	ln -s /etc/apache2/sites-available sites-available

add_user:
	@echo "ADDING USER MARCO..."
	adduser marco

verify_system:
	systemctl status apache2
	systemctl is-active mysql

install_server: install_apache install_php install_apache_ssl install_mysql install_composer install_npm add_user
	@echo "Apache Log dir: /var/log/apache2"
	@echo "Now modify /etc/apache2/sites-available/000-default.conf or add some config\n\n"
	@echo "remember a2ensite new sites!"

create_backup_structure:
	mkdir /var/databaseDump
	chmod 777 /var/databaseDump

backup_database:
	mysqldump databaseToBackup -uroot -p >> /var/databaseDump/database.sql