# Raspberry Pi Setup, docker install and TIG stack installation

## Requirements (for Linux - debian based):

Install Ansible by using virtualenv (to not mess up possible different python environments)
1. Install sshpass, pip and virtualenv  
- `sudo apt install -y sshpass python3-pip python3-venv`
2. Create your virtual environment, in this case I named it ansible
- `python3 -m venv ansible`
3. Activate your virtual environment
- `source ansible/bin/activate`
4. Install ansible in your virtual environment
- `python3 -m pip install ansible`
5. Check the version
- `ansible --version`
6. Clone this git repository 
- `https://github.com/nieldejonghe/rpi-ansible.git'
7. CD to the ansible directory 
- `cd rpi-ansible`
8. Install the requirements for ansible with ansible galaxy 
- `ansible-galaxy collection install -r requirements.yml`
9. Edit the config.yml file with the desired parameters and rename it to config.yml
10. Now you can run the playbooks.

## pi-setup playbook:
### This playbook will initialize the pi with the settings you configured in the config.yml file and install docker and docker-compose with pip.
### Requirements:

1. Install the Raspberry Pi Imager on a computer with an SD Card available
2. Download latest OS (In my case Bullseye 64bit) 
https://downloads.raspberrypi.org/raspios_arm64/images/raspios_arm64-2021-11-08/
3. Extract the zip, Start the Raspberry Pi Imager and select the OS and Storage and press Write
4. After you flashed the SD card, Navigate to the boot directory of the SD card and create a new empty file named 'ssh'
5. On boot Pi will check if this file exists, if it does SSH will be enabled and the file is removed 

the RPI4 doesn't come with SSH enabled as standard, you can enabled SSH with:

1. Headless mode: Insert the SD card into a computer and add a file called 'ssh' to the boot drive
2. Connect monitor + keyboard and mouse, go to preferences > configuration > enable SSH 

Now you can connect to the rpi with pi@raspberry.local (default hostname) 
Or you can edit /etc/dhcpd.conf on the root (ext4) partition on your SD card

- Run the playbook pi-setup, this will initialize the pi with the settings you configured and install docker and docker-compose.
`ansible-playbook pi-setup.yml --ask-pass --diff`

## pi-tig stack playbook:
### Will setup a Telegraf, InfluxdDB, Pihole and grafana stack on the raspberry pi.
### Requirements:

- Docker and docker-compose are installed on the raspberry pi, you can do this manually or with the playbooks in the repository.

- Run the playbook pi-tig-stack.yml, since we already ran the setup playbook the passwords is the one we set in the config.yml file previously
`ansible-playbook pi-stack.yml --ask-pass --diff`

### Troubleshooting tips: 

- You can use `docker logs <containername>` to see the logs of a container.

- Use `docker exec -it <containername> /bin/bash' to drop into the docker with a bash shell.

- You can use `docker exec -it influxdb influx` to instantly drop into the influx cli in your influxdb container. After that you can authenticate with `auth` and use the credentials you configured in the config.xml file. Drop into the database with `use <dbname>` and show test if the db contains data with `select * from system limit 5`.





