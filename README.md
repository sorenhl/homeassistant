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
Generate ssl key 

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
