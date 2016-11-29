# homeassistant


Remember on Raspberry:
Timezone:
apt-get install ntpdate
sudo /etc/init.d/ntp stop
sudo ntpd -q -g
sudo /etc/init.d/ntp start
sudo cp /usr/share/zoneinfo/Europe/Copenhagen  /etc/localtime
