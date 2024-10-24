# Install Instana Server on Ubuntu 22.04 #

# Install Docker #
## Setup Docker’s apt Repository ##
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Install Docker Latest Packages ##
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Check Version Docker ##
```
docker -v
```

##  create the docker group and add your user ##
1. Create the docker group.
```
sudo groupadd docker
```

2. Add your user to the docker group.
```
sudo usermod -aG docker $USER
```

3. run the following command to activate the changes to groups:
```
newgrp docker
```

## Configure Docker to start on boot with systemd #
```
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```


# Install Instana Server #

## Create directory instana ##
```
sudo su
```
```
mkdir /opt/instana
```
```
cd /opt/instana
```

## Export Variable DOWNLOAD_KEY ##
replace <DOWNLOAD_KEY> with your own
```
export DOWNLOAD_KEY= < DOWNLOAD_KEY>
```

## Configure to download and install from Debian Instana repository safely ##
```
echo 'deb [signed-by=/usr/share/keyrings/instana-archive-keyring.gpg] https://artifact-public.instana.io/artifactory/rel-debian-public-virtual generic main' > /etc/apt/sources.list.d/instana-product.list
```

## Add repository ##
```
cat << EOF > /etc/apt/auth.conf
machine artifact-public.instana.io
  login _
  password $DOWNLOAD_KEY
EOF

wget -nv -O- --user=_ --password="$DOWNLOAD_KEY" https://artifact-public.instana.io/artifactory/api/security/keypair/public/repositories/rel-debian-public-virtual | gpg --dearmor > /usr/share/keyrings/instana-archive-keyring.gpg
```

## Install instana-console ##
```
apt update -y
```
```
#Version Current
apt install instana-console
```
```
#Spesifik Version
apt install instana-console=277-1-1
```

## To avoid major updates during the upgrade process ##
```
cat > /etc/apt/preferences.d/instana-console <<EOF
Package: instana-console
Pin: version 277-1-1
Pin-Priority: 1000
EOF
```

## Create directory ##
```
sudo mkdir /mnt/{data,metrics,traces}
```

## Install the Instana backend on your machine by running the following command ##
```
instana init
```

### Information: ###
- Installation Type: Select single
- Tenant Name: fauzi
- Unit Name: labs
- Agent Key: < agent key>
- Download Key: <download key>
- Sales Key: < sales key>
- DNS name/Domain: <use IP VM> or Domain
- Data be stored: /mnt/data
- Trace data be stored: /mnt/metrics
- Metric data be stored: /mnt/traces
- Logs be stored: /var/log/instana
- Path signed certificate: - (if using Domain)
- Path private key: - (if using Domain)

# Activation License #

## Download license ##
```
sudo instana license download
```
## Now import it ##
```
sudo instana license import -f license
```
## Download license ##
```
sudo instana license verify
```

