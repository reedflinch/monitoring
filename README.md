### Log container data packets to Elasticsearch/Kibana

Based on article : http://agonzalezro.github.io/log-your-docker-containers-from-a-container-with-packetbeat.html

The packetbeat containers log directly to Elasticsearch and store logs locally under /tmp/packetbeat.

###Docker compose

Installation instructions : https://docs.docker.com/compose/

To start Elasticsearch, Kibana and Packetbeat using docker-compose run :
```
docker-compose up -d packetbeat
```

### Manually setting up each container individually

#### Start ElasticSearch Container
```
docker run -d -p 9200:9200 -p 9300:9300 --name elasticsearch elasticsearch:latest
```

#### Start Kibana Container
```
docker run -d -p 5601:5601 --name kibana --link elasticsearch:elasticsearch -e ELASTICSEARCH_URL=http://elasticsearch:9200 kibana:latest
```

#### Build Packetbeat image 

The configuration for packetbeat is in the packetbeat.yml file.

```
docker build --pull -t logging/packetbeat .
```

#### Start Packetbeat container
```
docker run -d --restart=always --net=host --name pb logging/packetbeat
```

#### Start test container to create data
```
docker-compose up -d test
```

Open Browser : http://localhost:5601

Change index pattern from :

logstash-*

to

packetbeat-*

Other useful commands :
```
docker-compose ps
docker-compose logs packetbeat
```
Start some test containers to test the logging is working :
```
docker-compose scale test=10
```

Teardown everything :
```
docker-compose stop
docker-compose rm -f
```
