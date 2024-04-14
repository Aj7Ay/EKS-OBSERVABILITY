# Installation

Prometheus container
1. Copy prometheus.yml to server

''' docker run -d --name prometheus-container -v /home/ubuntu/prometheus.yml:/etc/prometheus/prometheus.yml -e TZ=UTC -p 9090:9090 ubuntu/prometheus:2.50.0-22.04_stable '''
