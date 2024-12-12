Installing and Setting up the Prometheus Server
```
cd ..
sudo wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz
sudo tar xvf prometheus-2.53.0.linux-amd64.tar.gz
cd prometheus-2.53.0.linux-amd64/
./prometheus-2.53.0.linux-amd64/prometheus
```
Go to web browser :   http://you_vm_ip:9090

Run your first query “up” > hit Execute -  result up{instance="localhost:9090", job="prometheus"}
