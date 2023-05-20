pip3 install openshift pyyaml kubernetes --user
pip install netaddr
curl -L https://github.com/projectcalico/calico/releases/download/v3.25.0/calicoctl-linux-amd64 -o kubectl-calico
chmod +x kubectl-calico
pip install jmespath



docker run -p 8080:8080 -e KAFKA_CLUSTERS_0_NAME=local -e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=192.168.58.10:9092,192.168.58.11:9092,192.168.58.12:9092 -d provectuslabs/kafka-ui:latest



docker run -p 8080:8080 \
	-e KAFKA_CLUSTERS_0_NAME=local \
	-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=42.117.246.30:9091 \
	-d provectuslabs/kafka-ui:latest


docker run -d  --name maxscale    -p 8989:8989  -v $PWD/maxscale.cnf:/etc/maxscale.cnf  mariadb/maxscale

docker run -d  --name maxscale    -p 8989:8989    mariadb/maxscale