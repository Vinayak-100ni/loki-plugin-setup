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

