## ELK Stack Components

### Elasticsearch

Elasticsearch is a powerful search and analytics engine based on the Apache Lucene library. It is responsible for storing and indexing the log data that Logstash processes.

In this setup, Elasticsearch is configured as a single-node cluster using the `elasticsearch:7.16.2` image. The container is named `elasticsearch`, and it restarts automatically in case of any issues. The Elasticsearch data is stored in a Docker volume named `elastic_data`, which persists the data across container restarts. The service exposes ports 9200 and 9300 for HTTP and TCP communication, respectively. The single-node configuration is specified using the `discovery.type: single-node` environment variable.

### Logstash

Logstash is a data processing pipeline that ingests, processes, and forwards logs to Elasticsearch. It is highly configurable and supports a wide range of input, filter, and output plugins.

In this configuration, Logstash is set up using the `logstash:7.16.2` image. The container is named `logstashv2` and restarts automatically. Logstash reads the Apache log files from the `./logs/` directory on the host machine and uses the provided `logstash.conf` file for its input and output configurations. The service depends on the Elasticsearch service to be available before starting.

The `logstash.conf` file specifies an input plugin to read Apache log files from the `/logs/` directory and an output plugin to send the log data to Elasticsearch at http://elasticsearch:9200. The input plugin configuration includes the `start_position` set to "beginning" and the `sincedb_path` set to "/dev/null" to ensure that Logstash reads the entire log file every time it starts.

### Kibana

Kibana is a visualization and exploration tool for Elasticsearch data. It provides a user-friendly interface to create visualizations, dashboards, and perform advanced analytics on the indexed data.

In this setup, Kibana is configured using the `kibana:7.16.2` image. The container is named `kibana` and restarts automatically. Kibana communicates with Elasticsearch via the specified URL (http://elasticsearch:9200) and exposes port 5601 for accessing the Kibana dashboard. The service depends on the Elasticsearch service to be available before starting.

We create an index pattern to retrieve logstash parse requests.
![index pattern creation](https://github.com/Rorschach-Lone/Siem/blob/main/image/Pasted%20image%2020230401160634.png)
Here we check that our index retrieves the request.
![number of request got](https://github.com/Rorschach-Lone/Siem/blob/main/image/Pasted%20image%2020230401160846.png)
Finally we can visualise the requests in kibana.
![kibana visualisation](https://github.com/Rorschach-Lone/Siem/blob/main/image/image.png)
### Apache Log Generator

The Apache log generator is a custom Docker service that generates Apache log files using the `mingrammer/flog` image. The container is named `apache_log_generator` and restarts automatically. The generator creates Apache logs using the "apache_common" format and writes them to the `/logs/apache.log` file. The logs are stored in the `./logs/` directory on the host machine, which is also mounted as a volume in the Logstash container.

## Functionality

With this ELK stack setup, the Apache log generator service creates log files that are read by Logstash. Logstash processes the log data and forwards it to Elasticsearch for indexing and storage. Kibana then provides a user interface to visualize and analyze the log data stored in Elasticsearch.

This configuration demonstrates a simple yet powerful use case for the ELK stack to collect, process, and analyze log data from various sources, enabling better insights and faster issue resolution.
