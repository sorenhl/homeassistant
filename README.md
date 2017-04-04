# homeassistant


## Setup
Remember ssh file to allow ssh without screen
Update:
```sh
sudo apt-get update
sudo apt-get upgrade
```
Timezone:
```sh
apt-get install ntpdate
sudo /etc/init.d/ntp stop
sudo ntpd -q -g
sudo /etc/init.d/ntp start
sudo cp /usr/share/zoneinfo/Europe/Copenhagen  /etc/localtime
```


### Home-assistant
```sh
sudo apt-get install python3 python3-venv python3-pip
sudo pip3 install homeassistant
```
Create secrets.yaml
Generate ssl key (openssl req -new -x509 -sha256 -newkey rsa:4096 -nodes -keyout privkey.pem -days 730 -out fullchain.pem)

### Install home-assistant z-wave
```sh
sudo apt-get install cython3 libudev-dev python3-sphinx python3-setuptools git
sudo pip3 install --upgrade cython==0.24.1
git clone https://github.com/OpenZWave/python-openzwave.git
cd python-openzwave
git checkout python3
PYTHON_EXEC=$(which python3) make build
sudo PYTHON_EXEC=$(which python3) make install
```

### Install zwave control panel
```sh
apt-get install libgnutls28-dev libgnutlsxx28
wget ftp://ftp.gnu.org/gnu/libmicrohttpd/libmicrohttpd-0.9.19.tar.gz
tar zxvf libmicrohttpd-0.9.19.tar.gz
mv libmicrohttpd-0.9.19 libmicrohttpd
cd libmicrohttpd
./configure
make
sudo make install
cd
git clone https://github.com/OpenZWave/open-zwave.git
cd open-zwave
make
cd
git clone https://github.com/OpenZWave/open-zwave-control-panel.git
cd open-zwave-control-panel
Modify make file
  OPENZWAVE := ../open-zwave
  LIBMICROHTTPD := /usr/local/lib/libmicrohttpd.a
```
Note that you may modify some more in the configuration file 

### Aditional installations
sudo apt-get install nmap


### ftp
```sh
sudo apt-get install pure-ftpd
```

### Start as a service
```sh
sudo su -c 'cat <<EOF >> /etc/systemd/system/home-assistant@pi.service
[Unit]
Description=Home Assistant
After=network.target

[Service]
Type=simple
User=%i
ExecStart=/usr/local/bin/hass

[Install]
WantedBy=multi-user.target
EOF'
sudo systemctl --system daemon-reload
sudo systemctl enable home-assistant@pi
sudo systemctl start home-assistant@pi
sudo systemctl status home-assistant@pi -l
 ```

### Sonos api
sudo pm2 startup systemd -u

### My SQL
https://home-assistant.io/components/recorder/
Install
```sh

# My SQL Server
sudo apt-get install mysql-server --fix-missing
sudo apt-get install python-mysqldb
sudo apt-get install && sudo pip3 install mysqlclient
sudo nano /etc/mysql/my.cnf - change bind-address to 0.0.0.0
sudo service mysql restart
mysql -uroot -hlocalhost -p
```
SQL Commands:
```sql
CREATE DATABASE homeassistant;
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('');
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```



### My Sensors
#### Make make file work
edit configure file and add this code:

--my-mqtt-user=*)
    CPPFLAGS="-DMY_MQTT_USER=\\\"${optarg}\\\" $CPPFLAGS"
    ;;
--my-mqtt-password=*)
    CPPFLAGS="-DMY_MQTT_PASSWORD=\\\"${optarg}\\\" $CPPFLAGS"
    ;;
after this code:

--my-signing-request-gw-signatures-from-all*)
        signing_request_signatures=true
        CPPFLAGS="-DMY_SIGNING_GW_REQUEST_SIGNATURES_FROM_ALL $CPPFLAGS"
        ;;
this is about 408 number line

#### Configure
./configure --my-gateway=mqtt --my-controller-ip-address=127.0.0.1 --my-mqtt-publish-topic-prefix=mysensors-out --my-mqtt-subscribe-topic-prefix=mysensors-in --my-mqtt-client-id=mygateway1 --my-mqtt-user=mysensors --my-mqtt-password=<password> --my-transport=nrf24 --my-rf24-irq-pin=15

sudo systemctl start mysgw.service

#### Debug
./bin/mysgw -d

### MQTT
start, stop: /etc/init.d/mosquitto start|stop
debug: /usr/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf

password:
mosquitto_passwd -b pwfile mysensors <password>

### Plex
https://plex.tv/devices.xml

### homebridge
1. Install homebridge
2. cd. /usr/local/lib/node_modules
3. sudo git clone https://github.com/sorenhl/homebridge-homeassistant
4. cd homebridge-homeassistant && sudo npm install
5. cp homebridge/config-sample.json /home/pi/.homebridge/config.json
6. Copy from https://github.com/sorenhl/homebridge-homeassistant and remember to add dino.heidelarsen.dk in /etc/hosts to allow SSL certificate
7. pm2 start /usr/local/bin/homebridge -n Homebridge"

## Pi Others
sudo shutdown -h now