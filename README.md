# Elasticsearch plugin to analyze Scientific names

Normalizes scientific names into canonical names to provide a consistent search.


## Build
This project requires Java 17, since some external dependencies (Elasticsearch and Lucene) are built for that java version and is meant to be deployed 
into an 8.1.0 Elasticsearch cluster. 

Use `mvn clean package install verify`

## Installation
Copy the jar produced to an Elasticsearch server and run 

`/elasticsearch-plugin install checklistbank-elasticsearch-plugin.jar`

