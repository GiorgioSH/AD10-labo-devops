# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

@scriptM1 = <<SCRIPT
echo ""
echo ""
echo "               ___               "
echo "              (o,o)              "
echo "             <  .  >             "
echo "              -----              "
echo "+-------------------------------+"
echo "|          IT-AKADEMY           |"
echo "| VM LAB AD10 - Linux server 1  |"
echo "+-------------------------------+"
echo "\n"
echo "Mise à jour des paquets...\n"
sudo apt update &> /dev/null
sudo apt upgrade -y &> /dev/null

echo "Installation de Git...\n"
sudo apt install -y git-core &> /dev/null

echo "Installation d'Apache 2...\n"
sudo apt install -y apache2 &> /dev/null
echo "Activation des modules Apache...\n"
sudo a2enmod rewrite headers expires &> /dev/null
echo "Redémarrage d'Apache...\n"
sudo systemctl restart apache2 &> /dev/null

echo "Configuration de SSH\n"
sudo sed -e '/PasswordAuthentication no/ s/^#*/#/' -i /etc/ssh/sshd_config
sudo systemctl restart sshd

echo "Installation terminée. Connectez-vous avec la commande vagrant ssh\n"
echo "L'adresse IP locale de votre VM est 192.168.33.101\n"
echo "Le répertoire local est mappé dans /var/www/html\n"
echo "Connexion à la console : vagrant ssh web\n"
SCRIPT

@scriptM2 = <<SCRIPT
echo ""
echo ""
echo "               ___               "
echo "              (o,o)              "
echo "             <  .  >             "
echo "              -----              "
echo "+-------------------------------+"
echo "|          IT-AKADEMY           |"
echo "| VM LAB AD10 - Linux server 2|"
echo "+-------------------------------+"
echo "\n"
echo "Mise à jour des paquets...\n"
sudo apt update &> /dev/null
sudo apt upgrade -y &> /dev/null

echo "Installation de Git...\n"
sudo apt install -y git-core &> /dev/null

echo "Installation de MariaDb..."
sudo apt install -y software-properties-common dirmngr &> /dev/null
export DEBIAN_FRONTEND=noninteractive
sudo debconf-set-selections <<< 'mariadb-server mysql-server/root_password password 0000'
sudo debconf-set-selections <<< 'mariadb-server mysql-server/root_password_again password 0000'
sudo -E apt install -y mariadb-server &> /dev/null
sudo mysql -uroot -p0000 -e  "CREATE DATABASE ad10;"
sudo mysql -uroot -p0000 -e  "CREATE USER 'ad10' IDENTIFIED BY '0000';"
sudo mysql -uroot -p0000 -e "GRANT USAGE ON *.* TO 'ad10'@localhost IDENTIFIED BY '0000';"
sudo mysql -uroot -p0000 -e "GRANT ALL privileges ON ad10.* TO 'ad10'@localhost;"
sudo mysql -uroot -p0000 -e  "FLUSH PRIVILEGES;"
echo "Le mot de passe root de MariaDb est : 0000"
echo "La base de données 'ad10' a été créée"
echo "L'utilisateur MariaDB 'ad10' a été créé"
echo "Le mot de passe de l'utilisateur MariaDb 'ad10' est : 0000 \n"

echo "Configuration de SSH\n"
sudo sed -e '/PasswordAuthentication no/ s/^#*/#/' -i /etc/ssh/sshd_config
sudo systemctl restart sshd

echo "Installation terminée. Connectez-vous avec la commande vagrant ssh\n"
echo "L'adresse IP locale de votre VM est 192.168.33.102\n"
echo "Le répertoire local est mappé dans /var/www/html\n"
echo "Connexion à la console : vagrant ssh db1\n"
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "web" do |web|
    web.vm.box = 'debian/buster64'
  	web.vm.box_check_update = true
    web.vm.network "private_network", ip: "192.168.33.101"
  	web.vm.synced_folder "../data", "/vagrant_data", disabled: true
    web.vm.synced_folder ".", "/var/www/html", owner: "vagrant", group: "www-data"
    web.vm.provision 'shell', inline: @scriptM1

    web.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--name", "LAB AD10 - S2 - WEB"]
  		vb.gui = false
    end
  end

  config.vm.define "db1" do |db1|
    db1.vm.box = 'debian/buster64'
  	db1.vm.box_check_update = true
    db1.vm.network "private_network", ip: "192.168.33.102"
  	db1.vm.synced_folder "../data", "/vagrant_data", disabled: true
    db1.vm.synced_folder ".", "/var/www/html", owner: "vagrant", group: "www-data"
    db1.vm.provision 'shell', inline: @scriptM1

    db1.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--name", "LAB AD10 - S2 - DB"]
  		vb.gui = false
    end
  end

end
