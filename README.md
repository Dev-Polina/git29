cd /media/$USER/VBox_GAs_7.2.4
sudo sh ./VBoxLinuxAdditions.run

sudo apt update && sudo apt upgrade -y
sudo ubuntu-drivers autoinstall
sudo apt install -y kmod sysfsutils openssh-server xrdp
sudo systemctl enable --now ssh xrdp
sudo systemctl restart NetworkManager
sudo apt install -y git libreoffice
sudo apt install -y cups cups-pdf
sudo systemctl enable --now cups
ss -tuln | grep -E '22|3389'
ip a && ping -c 2 8.8.8.8 && lpstat -p -d

# Резервное копирование конфигураций (папка /etc) 
sudo tar -czf /sys_backup.tar.gz /etc 
# Создание установочного образа загрузчика (MBR диска sda) 
sudo dd if=/dev/sda of=/system_boot.img bs=512 count=1 
# Создание точки восстановления (делаем быстрый снимок домашней папки и состояния важных утилит) 
sudo tar -czf /sys_snapshot.tar.gz /home /var/backups
ls -lh /sys_backup.tar.gz /system_boot.img /sys_snapshot.tar.gz
# Создание группы и пользователя с домашним каталогом
sudo groupadd group1 && sudo useradd -m -g group1 user1
# Создание защищенной папки, смена владельца на root:group1 и жесткие права 770
sudo mkdir -p /opt/secure && sudo chown -R root:group1 /opt/secure && sudo chmod -R 770 /opt/secure
ls -ld /opt/secure
# Установка сложного пароля пользователю через PAM-модули
echo "user1:Zh3756Kb" | sudo chpasswd
# Ограничение прав на журнал аутентификации (чтобы посторонние не видели логи входов)
sudo chmod 640 /var/log/auth.log
# Запуск и перезапуск службы мониторинга и аудита rsyslog
sudo systemctl enable --now rsyslog && sudo systemctl restart rsyslog
ls -l /var/log/auth.log && sudo systemctl status rsyslog --no-pager
