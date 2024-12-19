sudo nano install_docker.sh
```bash
#!/bin/bash
DOCKER_COMPOSE_VERSION="1.29.2"
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce
sudo usermod -aG docker $USER
sudo curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker --version
docker-compose --version
echo "Please log out and log back in to apply the Docker group changes, or run: newgrp docker"
chmod +x install_docker.sh
./install_docker.sh
mkdir log-processing-stack
cd log-processing-stack
sudo useradd -r -s /bin/false prometheus
sudo mkdir /prom_data
sudo mkdir /etc/prometheus
sudo chown prometheus:prometheus /prom_data
cd prometheus-2.51.1.linux-amd64/
sudo mv console* /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/
sudo chown -R prometheus:prometheus /etc/prometheus/
sudo chown -R prometheus:prometheus /usr/local/bin/prom*
sudo mv prome* /usr/local/bin/
sudo chown -R prometheus:prometheus /usr/local/bin/prom*
sudo curl -SL https://github.com/docker/compose/releases/download/v2.26.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
ls -la /usr/local/bin/docker-compose
sudo chown -R prometheus:prometheus /usr/local/bin/prom*
sudo chown -R prometheus:prometheus /etc/prometheus/
sudo chown nodeexporter:nodeexporter /usr/local/bin//node_exporter
sudo useradd -r -s /bin/false nodeexporter
sudo chown nodeexporter:nodeexporter /usr/local/bin//node_exporter
ls -la /usr/local/bin/
sudo usermod -aG docker ${USER}
sudo -i
chmod +x install_docker.sh
ls
chmod +x install.sh
./install.sh
docker --version
docker-compose  --version
docker-compose up -d
cd docker-compose/
prometheus/
ls
docker-compose up -d
cd prometheus/
docker-compose up -d
sudo usermod -aG docker $USER
sudo chmod +x /usr/local/bin/docker-compose
sudo systemctl status docker
sudo systemctl restart docker
sudo usermod -aG docker markus
sudo newgroup docker
'newgroup docker
;
newgroup docker
groups markus
exit
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
ls -la /usr/local/bin/docker-compose
docker-compose --version
sudo mkdir /etc/prometheus
cd /etc/prometheus/
ls
ls -la
sudo nano prometheus.yml
docker-compose up -d
docker-compose up -d   #docker compose up -d
docker-compose up -d
docker compose up -d
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt install docker-ce
vim install_docker.sh
chmod +x install_docker.sh
exit
