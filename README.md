# ELK Stack -  ElasticSearch, Logstash, and Kibana

## Updated to version 7.6.1

All stack has been updated to version 7.6.1

## Get Started
Do you wanna a simple way to raise a ELK stack?

Just pull this repository:

`git clone https://github.com/shimatai/docker-elk.git`

Then, run `docker-compose` to create docker instances:

```
$ cd docker-elk
$ docker-compose up
```

By default, this stack exposes the following ports:

5000 (TCP): Logstash input listener
9200 (TCP): Elasticsearch REST API
9300 (TCP): Elasticsearch TCP nodes communication
5601 (TCP): Kibana web UI

## Tutorial from BogoToBogo
[Docker ELK : ElasticSearch, Logstash, and Kibana](https://www.bogotobogo.com/DevOps/Docker/Docker_ELK_ElasticSearch_Logstash_Kibana.php)
