# ELK Stack -  ElasticSearch, Logstash and Kibana

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

Then, access Kibana web UI at http://localhost:5601

After getting into Kibana web console, you will get the message "Create index pattern - Couldn't find any Elasticsearch data", then you should run this command in terminal:

```
curl -XPOST -D- 'http://localhost:5601/api/saved_objects/index-pattern' \
 -H 'Content-Type: application/json' \
 -H 'kbn-version: 7.6.1' \
 -d '{"attributes":{"title":"logstash-*","timeFieldName":"@timestamp"}}'
```

This request will create a default index in Kibana, so you application can send data to localhost:5000 (logstash TCP port for incoming log data).

By default, this stack exposes the following ports:

5000 (TCP): Logstash input listener

9200 (TCP): Elasticsearch REST API

9300 (TCP): Elasticsearch TCP nodes communication

5601 (TCP): Kibana web UI

## Tutorial from BogoToBogo
[Docker ELK : ElasticSearch, Logstash, and Kibana](https://www.bogotobogo.com/DevOps/Docker/Docker_ELK_ElasticSearch_Logstash_Kibana.php)
