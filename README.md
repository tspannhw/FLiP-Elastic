## FLiP-Elastic

Get your Apache Pulsar + Elastic 

### Command Line Setup

````
docker network ls
docker network inspect elastic

curl -s http://pulsar1:9200/my_index/_search -u elastic:changeme
curl -s http://pulsar1:9200/my_index/_refresh -u elastic:changeme

docker logs -f docker-elk_elasticsearch_1
docker logs -f docker-elk_kibana_1

docker-compose up -d

docker ps
docker-compose ps

docker-compose down
````

### References

* https://pulsar.apache.org/docs/en/io-elasticsearch/
* https://www.youtube.com/watch?v=7IstxhL8p9E
* https://www.elastic.co/guide/en/kibana/current/docker.html
* https://www.elastic.co/blog/docker-networking
* https://www.elastic.co/guide/en/elasticsearch/reference/7.16/security-minimal-setup.html
* https://github.com/elastic/stack-docs/blob/main/docs/en/getting-started/get-started-docker.asciidoc
* https://www.elastic.co/guide/en/elasticsearch/reference/7.16/docker.html
* https://pulsar.apache.org/docs/en/io-elasticsearch-sink/
* https://github.com/deviantony/docker-elk

