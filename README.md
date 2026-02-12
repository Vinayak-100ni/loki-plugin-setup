# loki-plugin-setup

🚀 STEP 1 — Install Loki on Ubuntu (Binary)

Download Loki:

wget https://github.com/grafana/loki/releases/latest/download/loki-linux-amd64.zip
unzip loki-linux-amd64.zip
chmod +x loki-linux-amd64
sudo mv loki-linux-amd64 /usr/local/bin/loki


Create config:

sudo nano /etc/loki-config.yaml


Paste minimal config:

auth_enabled: false

server:
  http_listen_port: 3100

common:
  path_prefix: /var/lib/loki
  storage:
    filesystem:
      chunks_directory: /var/lib/loki/chunks
      rules_directory: /var/lib/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2024-01-01
      store: boltdb-shipper
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h

limits_config:
  allow_structured_metadata: false


Create directories:

sudo mkdir -p /var/lib/loki


Start Loki:

loki -config.file=/etc/loki-config.yaml


🚀 STEP 2 — Install Grafana on Ubuntu
sudo apt install -y apt-transport-https software-properties-common
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://packages.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.key
echo "deb [signed-by=/etc/apt/keyrings/grafana.key] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list

sudo apt update
sudo apt install grafana


Start Grafana:

sudo systemctl enable grafana-server
sudo systemctl start grafana-server


🚀 STEP 3 — Install Docker Loki Logging Plugin
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions


Verify:

docker plugin ls


🚀 STEP 4 — Run Containers Using Loki Driver

First find your server IP:

hostname -I


Assume it is:

192.168.1.100


Now run container:

docker run -d \
  --name nginx \
  --log-driver=loki \
  --log-opt loki-url="http://192.168.1.100:3100/loki/api/v1/push" \
  nginx


  (Make Loki Default Driver for All Containers)

Edit:

sudo nano /etc/docker/daemon.json


Add:

{
  "log-driver": "loki",
  "log-opts": {
    "loki-url": "http://192.168.1.100:3100/loki/api/v1/push"
  }
}


Restart Docker:

sudo systemctl restart docker

