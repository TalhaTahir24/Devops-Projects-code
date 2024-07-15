# < Docker Swarm Monitoring Stack >
## Docker-Compose.yml
### For Grafana , Prometheus and Node exporter .  

```
version: '3.7'

services:
  node-exporter:
    image: prom/node-exporter
    ports:
      - target: 9100
        published: 9100
        mode: host
    deploy:
      mode: global
 

  cadvisor:
    image: gcr.io/cadvisor/cadvisor
    ports:
      - target: 8080
        published: 8080
        mode: host
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro
    deploy:
      mode: global
      resources:
        limits:
          cpus: '1'
          memory: 1G
        reservations:
          cpus: '0.25'
          memory: 512M

  prometheus:
    image: prom/prometheus:latest
    ports:
      - target: 9090
        published: 9090
        mode: host
    volumes:
      - /home/srvadmin/template/prometheus_data:/prometheus
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    deploy:
      placement:
        constraints:
          - node.role == manager
      replicas: 1

  grafana:
    image: grafana/grafana:latest
    ports:
      - target: 3000
        published: 3000
        mode: host
    volumes:
      - /home/srvadmin/template/grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
    deploy:
      replicas: 1
      placement:
        constraints:
          - node.role == manager

```


