# lấy metric kakfa, vậy cần kafka jmx ko nhỉ?
docker run -ti --rm -p 9308:9308 danielqsj/kafka-exporter --kafka.server=10.20.25.167:9092 --kafka.server=10.20.30.18:9092 --kafka.server=10.20.35.11:9092 

# kafka-ui
docker run -p 8080:8080 \
	-e KAFKA_CLUSTERS_0_NAME=local \
	-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=10.15.132.1:9092,10.15.132.2:9092,10.15.132.3:9092 \
	-d provectuslabs/kafka-ui:latest
