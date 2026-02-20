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
