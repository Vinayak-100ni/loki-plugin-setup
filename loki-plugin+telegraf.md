Install Loki Docker Logging Driver Plugin

```bash
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
docker plugin ls
```
Configure Docker to Use Loki by Default (Optional but Recommended)

```bash
sudo nano /etc/docker/daemon.json
```
```bash
{
  "log-driver": "loki",
  "log-opts": {
    "loki-url": "https://YOUR-LOKI-URL/loki/api/v1/push",
    "loki-batch-size": "400",
    "loki-retries": "5",
    "loki-external-labels": "host=new-server,env=prod"
  }
}
```
```bash
sudo systemctl restart docker
```



install telegraf

```bash
# Add the InfluxData GPG key
 
wget -q https://repos.influxdata.com/influxdata-archive.key
gpg --show-keys --with-fingerprint --with-colons ./influxdata-archive.key | grep -E '24C975CBA61A024EE1B631787C3D57159FC2F927|9D539D90D3328DC7D6C8D3B9D8FF8E1F7DF8B07E'
cat ./influxdata-archive.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive.gpg > /dev/null
 
# Add the repository
 
echo "deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive.gpg] https://repos.influxdata.com/debian stable main" | sudo tee /etc/apt/sources.list.d/influxdata.list
 
# Install Telegraf
 
sudo apt-get update && sudo apt-get install telegraf
```
```bash
# Restart the service

sudo systemctl restart telegraf
 
# Check status

sudo systemctl status telegraf
```
```bash
sudo -u telegraf telegraf --test --config /etc/telegraf/telegraf.conf --config-directory /etc/telegraf/telegraf.d
```

Enable Docker Input in Telegraf
```bash
sudo nano /etc/telegraf/telegraf.conf
```
```bash
[[inputs.docker]]
  endpoint = "unix:///var/run/docker.sock"
  gather_services = false
  timeout = "5s"
```
```bash
sudo usermod -aG docker telegraf
sudo systemctl restart telegraf
```
```bash
[[outputs.prometheus_client]]
  listen = ":9273"
  path = "/metrics"
```
```bash
sudo systemctl restart telegraf
```
