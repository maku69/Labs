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

```
# Adding Logstash and Filebeat to Docker-based Elastic Stack

This guide assumes you already have Elasticsearch and Kibana running in Docker containers on the `elastic` network, with security disabled for testing purposes.

## Prerequisites
- Docker and Docker Compose installed
- Elasticsearch container running (named `es01`)
- Kibana container running (named `kib01`)
- Docker network `elastic` created

## 1. Setting Up Logstash

### 1.1. Create Configuration Directory
```bash
# Create directory for Logstash configs
mkdir -p ~/elk/logstash/config
cd ~/elk/logstash/config
```

### 1.2. Create Logstash Configuration Files

#### Create pipeline configuration file
```bash
# Create logstash.yml
cat << EOF > logstash.yml
http.host: "0.0.0.0"
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.hosts: ["http://es01:9200"]
EOF
```

#### Create input configuration
```bash
# Create 02-beats-input.conf
cat << EOF > 02-beats-input.conf
input {
  beats {
    port => 5044
    host => "0.0.0.0"
  }
}
EOF
```

#### Create filter configuration
```bash
# Create 10-syslog-filter.conf
cat << EOF > 10-syslog-filter.conf
filter {
  if [container][name] {
    mutate {
      add_field => {
        "container_name" => "%{[container][name]}"
      }
    }
  }
  
  if [message] {
    grok {
      match => { "message" => "%{GREEDYDATA:log_message}" }
    }
  }
}
EOF
```

#### Create output configuration
```bash
# Create 30-elasticsearch-output.conf
cat << EOF > 30-elasticsearch-output.conf
output {
  elasticsearch {
    hosts => ["http://es01:9200"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
  }
}
EOF
```

### 1.3. Start Logstash Container
```bash
docker run -d \
  --name logstash01 \
  --net elastic \
  -v ~/elk/logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml \
  -v ~/elk/logstash/config:/usr/share/logstash/pipeline/ \
  -p 5044:5044 \
  docker.elastic.co/logstash/logstash:8.17.0
```

### 1.4. Verify Logstash is Running
```bash
# Check container status
docker ps | grep logstash01

# Check logs for any errors
docker logs logstash01
```

## 2. Setting Up Filebeat

### 2.1. Create Configuration Directory
```bash
# Create directory for Filebeat configs
mkdir -p ~/elk/filebeat/config
cd ~/elk/filebeat/config
```

### 2.2. Create Filebeat Configuration
```bash
# Create filebeat.yml
cat << EOF > filebeat.yml
filebeat.inputs:
- type: container
  paths: 
    - '/var/lib/docker/containers/*/*.log'

processors:
  - add_docker_metadata:
      host: "unix:///var/run/docker.sock"

filebeat.config.modules:
  path: \${path.config}/modules.d/*.yml
  reload.enabled: false
  
output.logstash:
  hosts: ["logstash01:5044"]
  
logging.json: true
logging.metrics.enabled: false
EOF
```

### 2.3. Start Filebeat Container
```bash
docker run -d \
  --name filebeat01 \
  --net elastic \
  --user root \
  --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro" \
  --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
  --volume="~/elk/filebeat/config/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  docker.elastic.co/beats/filebeat:8.17.0
```

### 2.4. Verify Filebeat is Running
```bash
# Check container status
docker ps | grep filebeat01

# Check logs for any errors
docker logs filebeat01
```
