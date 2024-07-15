# Prometheus Configration
```
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['0.0.0.0:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: 
          - '10.220.5.103:9100'
          - '10.220.5.102:9100'
          - '10.220.5.178:9100'
```
