# Speedtest metrics to prometheus

A docker-compose stack to send Google Wifi router metrics to prometheus server

This combines the standard prometheus docker container image, and this Google Wifi exporter image: https://github.com/nysasounds/google-wifi-prometheus-exporter

The docker compose config looks like this:

```
version: '3.8'

networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data: {}

services:
  google-wifi:
    image: nysasounds/google-wifi-prometheus-exporter:0.0.1
    container_name: google-wifi
    restart: unless-stopped
    expose:
      - 9469
    networks:
      - monitoring
  prometheus:
    image: prom/prometheus:v2.43.0
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus.yaml:/etc/prometheus/prometheus.yaml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yaml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention=7d'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    networks:
      - monitoring
```

## Run

Just update the `prometheus.yaml` with the premetheus ingestion destination of your choice, and:
```
docker compose up
```
