# Elasticsearch plugin to analyze Scientific names

Normalizes scientific names into canonical names to provide a consistent search.


## Build
This project requires Java 21, since some external dependencies (Elasticsearch and Lucene) are built for that java version and is meant to be deployed 
into an 9.3.1Elasticsearch cluster. 

Use `mvn clean package install verify`

## Installation
Copy the zip produced by the assembly plugin, copy it to server and run a command like

`sudo ES_PATH_CONF=/etc/elasticsearch/species bin/elasticsearch-plugin install file:///tmp/checklistbank-elasticsearch-plugins.zip `

 - _ES_PATH_CONF_: points to the configuration directory of Elasticsearch node
 - If the plugin is installed from the file system the URI prefix "file:///" must be used

