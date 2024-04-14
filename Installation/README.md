# Installation

#### Prometheus container
1. Copy prometheus.yml to server
2. start container with below command

```
docker run -d --name prometheus-container -v /home/ubuntu/prometheus.yml:/etc/prometheus/prometheus.yml -e TZ=UTC -p 9090:9090 ubuntu/prometheus:2.50.0-22.04_stable
```

#### Grafana Container

```
docker run -d --name=grafana -p 3000:3000 grafana/grafana:10.4.2
```

username : admin
password : admin

#### Influx DB container

```
docker run -d -p 8086:8086 --name influxdb2 influxdb:1.8.6-alpine
```

**Connect to container and execute below Influx Commands**
```
docker exec -it influxdb2 bash 
influx
CREATE DATABASE "jenkins" WITH DURATION 1825d REPLICATION 1 NAME "jenkins-retention"
SHOW DATABASES
USE <database_name>
```

![image](https://github.com/Aj7Ay/EKS-OBSERVABILITY/assets/110721907/8bb85724-b7b4-4cbc-a35b-647870135e26)

# Queries

Jenkins health --> use prometheus data source
```
up{instance="<jenkins-ip>:8080", job="jenkins"}
```

Jenkins executor | node | queue count --> use prometheus data source

```
jenkins_executor_count_value
```

```
jenkins_node_count_value
```

```
jenkins_queue_size_value
```

Jenkins over all --> influxDB

```
SELECT count(build_number) FROM "jenkins_data" WHERE ("build_result" = 'SUCCESS' OR "build_result" = 'CompletedSuccess')

SELECT count(build_number) FROM "jenkins_data" WHERE ("build_result" = 'FAILURE' OR "build_result" = 'CompletedError' )

SELECT count(build_number) FROM "jenkins_data" WHERE ("build_result" = 'ABORTED' OR "build_result" = 'Aborted' )

SELECT count(build_number) FROM "jenkins_data" WHERE ("build_result" = 'UNSTABLE' OR "build_result" = 'Unstable' )
```

Pipelines ran in last 10 minutes --> influx DB

```
SELECT count(project_name) FROM jenkins_data WHERE time >= now() - 10m
```

Total Build --> influx DB

```
SELECT count(build_number) FROM "jenkins_data" 
```

Last Build status --> influx Db
```
SELECT build_result FROM "jenkins_data" WHERE $timeFilter  ORDER BY time DESC LIMIT 1
```

Average time 

```
select build_time/1000 FROM jenkins_data WHERE $timeFilter 
```

last 10 build details

```
SELECT "build_exec_time", "project_name", "build_number", "build_causer", "build_time", "build_result" FROM "jenkins_data" WHERE $timeFilter ORDER BY time DESC LIMIT 10
```
