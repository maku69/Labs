```bash
    4  sudo apt install docker
    5  # Add Docker's official GPG key:
    6  sudo apt-get update
    7  sudo apt-get install ca-certificates curl
    8  sudo install -m 0755 -d /etc/apt/keyrings
    9  sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
   10  sudo chmod a+r /etc/apt/keyrings/docker.asc
   11  # Add the repository to Apt sources:
   12  echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   13  sudo apt-get update
   14  sudo apt update
   15  sudo apt upgrade
   16  for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
   17  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   18  sudo docker run hello-world
   19  # Add Docker's official GPG key:
   20  sudo apt-get update
   21  sudo apt-get install ca-certificates curl
   22  sudo install -m 0755 -d /etc/apt/keyrings
   23  sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
   24  sudo chmod a+r /etc/apt/keyrings/docker.asc
   25  # Add the repository to Apt sources:
   26  echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   27  sudo apt-get update
   28  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   29  sudo docker run hello-world
   30  docker --version
   31  sudo apt-get update
   32  sudo apt-get install docker-compose-plugin
   33  docker compose version
   34  sudo sysctl -w vm.max_map_count=262144
   35  echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
   36  sudo sysctl -p
   37  docker network create elastic
   38  sudo docker network create elastic
   39  multipass info elastic-node
   40  sudo multipass info elastic-node
   41  docker pull docker.elastic.co/elasticsearch/elasticsearch:8.17.0
   42  sudo docker pull docker.elastic.co/elasticsearch/elasticsearch:8.17.0
   43  docker run --name es01 --net elastic -p 9200:9200   -e "discovery.type=single-node"   -e "xpack.security.enabled=false"   -d docker.elastic.co/elasticsearch/elasticsearch:8.17.0
   44  sudo docker run --name es01 --net elastic -p 9200:9200   -e "discovery.type=single-node"   -e "xpack.security.enabled=false"   -d docker.elastic.co/elasticsearch/elasticsearch:8.17.0
   45  curl -X GET "http://192.168.5.240:9200"
   47  multipass info elastic-node
   48  docker pull docker.elastic.co/kibana/kibana:8.17.0
   49  sudo docker pull docker.elastic.co/kibana/kibana:8.17.0
   50  docker run -d --name kib01 --net elastic -p 5601:5601   -e "ELASTICSEARCH_HOSTS=http://es01:9200"   docker.elastic.co/kibana/kibana:8.17.0
   51  sudo docker run -d --name kib01 --net elastic -p 5601:5601   -e "ELASTICSEARCH_HOSTS=http://es01:9200"   docker.elastic.co/kibana/kibana:8.17.0
