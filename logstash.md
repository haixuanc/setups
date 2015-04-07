## Log Indexer Setup

### Elasticsearch

1. Download elasticsearch-1.4.4.

2. Start elasticsearch server:

	```
	$ /path/to/elasticsearch/bin/elasticsearch
	```
3. Go to http://localhost:9200 to see if it's running.
	
#### Optional

Install Elasticearch-kopf plugin:

```
$ /path/to/elasticsearch/bin/plugin -install lmenezes/elasticsearch-kopf
``` 

Now, you can go to http://localhost:9200/_plugin/kopf to browse your Elasticsearch data, settings and mappings.

### Kafka

1. Download kafka-0.8.2.0.

	```
	$ cd /path/to/kafka
	```
2. Start zookeeper:

	```
	$ bin/zookeeper-server-start.sh config/zookeeper.properties
	```
3. Start one server:
	
	```
	$ bin/kafka-server-start.sh config/server.properties
	```
4. Create a topic `test`:
	
	```
	$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
	```
5. Verify that the topic is created:

	```
	$ bin/kafka-topics.sh --list --zookeeper localhost:2181
	```
6. Send some messages to the topic:
	
	```
	$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
	
	>> input one message and hit enter to send it
	```
7. Start a consumer to verify that messages are sent successfully:
	
	```
	$ bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
	```


### Logstash

1. Download logstash-1.4.2.
2. Install plugin logstash-kafka-0.7.0:
	
	```
	// Download logstash-kafka-0.7.0
	$ cd /path/to/logstash-kafka-0.7.0
	$ gem build logstash-kafka.gemspec
	$ cp logstash-kafka-0.7.0-java.gem /path/to/logstash
	$ cd /path/to/logstash
	$ GEM_HOME=vendor/bundle/jruby/1.9 GEM_PATH= java -jar vendor/jar/jruby-complete-1.7.11.jar -S gem install logstash-kafka-0.7.0-java.gem
	$ cp -R vendor/bundle/jruby/1.9/gems/logstash-kafka-*-java/lib/logstash/* lib/logstash/
	```

3. Create a configuration file, say `indexer.conf`. Fill in the following content:

	```
	input {
		kafka {
			topic_id => "test"
		}
	}
	output {
		elasticSearch {
			host => localhost
		}
		stdout {
			codec => rubydebug
		}
	}
	```
4. Start logstash server:

	```
	$ /path/to/logstash/bin/logstash -f indexer.conf
	```
5. Go to http://localhost:9200/_search?pretty to browse logs in Elasticsearch.
	
### Kibana

1. Download kibana-4.0.2.
2. Start server:

	```
	$ /path/to/kibana/bin/kibana
	``` 
3. Go to http://localhost:5601.

## Log Shipper Setup

1. Repeat "Log Indexer Setup -> Logstash -> step 1, 2" to install logstash and plugin logstash-kafka.
2. Create a configuration file, say `shipper.conf`. Fill in the following content:

	```
	input {
		file {
			path => [
				"/path/to/log/file-1",
				"/path/to/log/file-2",
				"/path/to/logs/file-*",
				...
			]
			start_position => "beginning"
			# sincedb_path => "/dev/null" # uncomment this line to forget about read history
		}
	}
	output {
		kafka {
			topic_id => "test"
		}
	}
	```
3. Start log shipper:

	```
	$ /path/to/logstash/bin/logstash -f shipper.conf
	```
	




	
	



	