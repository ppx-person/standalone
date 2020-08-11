

# elasticsearch
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.8.0

docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.8.0


# kibana 
docker pull docker.elastic.co/kibana/kibana:7.8.0

docker run --link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch -p 5601:5601 {docker-repo}:{version}

docker run --link 547cc8897765:elasticsearch -p 5601:5601 docker.elastic.co/kibana/kibana:7.8.0

547cc8897765
# entrance
http://129.204.59.139:9200/
http://129.204.59.139:5601/































