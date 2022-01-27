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
````

### Deploy Sink

````

bin/pulsar-admin sinks stop --tenant public --namespace default --name elasticsearch-sink
bin/pulsar-admin sinks delete --tenant public --namespace default --name elasticsearch-sink

bin/pulsar-admin sinks create --archive ./connectors/pulsar-io-elastic-search-2.9.1.nar --tenant public 
--namespace default 
--name elasticsearch-sink --sink-config-file conf/elastic-sink.conf 
--inputs persistent://public/default/iotjetsonjson --parallelism 1

bin/pulsar-admin sinks status --tenant public --namespace default --name elasticsearch-sink

{
  "numInstances" : 1,
  "numRunning" : 1,
  "instances" : [ {
    "instanceId" : 0,
    "status" : {
      "running" : true,
      "error" : "",
      "numRestarts" : 0,
      "numReadFromPulsar" : 6,
      "numSystemExceptions" : 0,
      "latestSystemExceptions" : [ ],
      "numSinkExceptions" : 0,
      "latestSinkExceptions" : [ ],
      "numWrittenToSink" : 6,
      "lastReceivedTime" : 1643308578837,
      "workerId" : "c-standalone-fw-127.0.0.1-8080"
    }
  } ]
}

bin/pulsar-admin sinks get --tenant public --namespace default --name elasticsearch-sink

{
  "tenant": "public",
  "namespace": "default",
  "name": "elasticsearch-sink",
  "className": "org.apache.pulsar.io.elasticsearch.ElasticSearchSink",
  "sourceSubscriptionPosition": "Latest",
  "inputs": [
    "persistent://public/default/iotjetsonjson"
  ],
  "inputSpecs": {
    "persistent://public/default/iotjetsonjson": {
      "isRegexPattern": false,
      "schemaProperties": {},
      "consumerProperties": {},
      "poolMessages": false
    }
  },
  "configs": {
    "password": "changeme",
    "elasticSearchUrl": "http://pulsar1:9200",
    "indexName": "my_index",
    "username": "elastic"
  },
  "parallelism": 1,
  "processingGuarantees": "ATLEAST_ONCE",
  "retainOrdering": false,
  "autoAck": true
}

tail -500 logs/functions/public/default/elasticsearch-sink/elasticsearch-sink-0.log 

2022-01-27T12:49:43,662-0500 [pulsar-client-io-1-2] INFO  org.apache.pulsar.client.impl.ConsumerImpl - [persistent://public/default/iotjetsonjson][public/default/elasticsearch-sink] Subscribed to topic on 127.0.0.1/127.0.0.1:6650 -- consumer: 0
2022-01-27T13:26:12,202-0500 [pulsar-client-io-1-2] INFO  com.scurrilous.circe.checksum.Crc32cIntChecksum - SSE4.2 CRC32C provider initialized
2022-01-27T13:26:12,239-0500 [public/default/elasticsearch-sink-0] INFO  org.apache.pulsar.client.impl.schema.AutoConsumeSchema - Configure topic schema \x00\x00\x00\x00\x00\x00\x00\x00 for topic persistent://public/default/iotjetsonjson : {"type":"record","name":"IoTMessage","namespace":"io.streamnative.examples.oauth2","fields":[{"name":"camera","type":["null","string"],"default":null},{"name":"cpu","type":"double"},{"name":"cputemp","type":["null","string"],"default":null},{"name":"cputempf","type":["null","string"],"default":null},{"name":"diskusage","type":["null","string"],"default":null},{"name":"filename","type":["null","string"],"default":null},{"name":"gputemp","type":["null","string"],"default":null},{"name":"gputempf","type":["null","string"],"default":null},{"name":"host","type":["null","string"],"default":null},{"name":"host_name","type":["null","string"],"default":null},{"name":"imageinput","type":["null","string"],"default":null},{"name":"ipaddress","type":["null","string"],"default":null},{"name":"macaddress","type":["null","string"],"default":null},{"name":"memory","type":"double"},{"name":"networktime","type":"double"},{"name":"runtime","type":["null","string"],"default":null},{"name":"systemtime","type":["null","string"],"default":null},{"name":"te","type":["null","string"],"default":null},{"name":"top1","type":["null","string"],"default":null},{"name":"top1pct","type":"double"},{"name":"uuid","type":["null","string"],"default":null}]}


http://pulsar1:9200/my_index

{"my_index":{"aliases":{},"mappings":{"properties":{"camera":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"cpu":{"type":"float"},"cputemp":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"cputempf":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"diskusage":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"filename":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"gputemp":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"gputempf":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"host":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"host_name":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"imageinput":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"ipaddress":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"macaddress":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"memory":{"type":"float"},"networktime":{"type":"float"},"runtime":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"systemtime":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"te":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"top1":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"top1pct":{"type":"float"},"uuid":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}}}},"settings":{"index":{"routing":{"allocation":{"include":{"_tier_preference":"data_content"}}},"number_of_shards":"1","provided_name":"my_index","creation_date":"1643307972382","number_of_replicas":"1","uuid":"fkczbchfTECNFdH-DKXYNg","version":{"created":"7160399"}}}}}

````

### To Shutdown

````
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

