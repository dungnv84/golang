apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
./aws/install -i /usr/local/aws-cli -b /usr/local/bin

# install kubectl 
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl



# test 
aws s3 cp awscliv2.zip s3://mykiot-opensearch

# install java version 8

sudo apt update
sudo apt install openjdk-8-jdk -y

wget https://artifacts.opensearch.org/logstash/logstash-oss-with-opensearch-output-plugin-7.16.3-macos-x64.tar.gz

tar -zxvf logstash-oss-with-opensearch-output-plugin-7.16.3-macos-x64.tar.gz
 cd logstash-7.16.3/
 ./bin/logstash-plugin install --preserve logstash-input-opensearch
 
nano  logstash-input-opensearch.conf 
 
input {
  opensearch {
    hosts       => "https://vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443"
    user        => "admin"
    password    => "Myk!0t#3Catalog"
    index       => "mk_products"
    size => 100
    scroll => "1m"
    docinfo => true
    schedule => "*/2 * * * *"
#   query       => "{ "query": { "match_all": {}} }"    
  }
}

output {
  elasticsearch {
    hosts => "http://10.15.133.21:9200"
    user => "elastic"
    password => "ror1UItXjimUbans9Ph6"
    index => "mk_products"
  }
}

input {
  opensearch {
    hosts       => "https://vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443"
    user        => "admin"
    password    => "Myk!0t#3Catalog"
    index       => "mk_products"
    query => '{ "query": { "term": { "retailerId": 891134 } } }'
    size => 10000
  }
}

output {
  elasticsearch {
    hosts => "http://10.15.133.21:9200"
    user => "elastic"
    password => "ror1UItXjimUbans9Ph6"
    index => "mk_products"
  }
}

elasticdump --input=https://admin:Myk%210t%233Catalog@vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443/mk_products  --output=http://elastic:ror1UItXjimUbans9Ph6@10.15.133.21:9200/mk_products    --type=settings

curl -XDELETE http://elastic:ror1UItXjimUbans9Ph6@10.15.133.21:9200/mk_product

elasticdump --input=https://admin:Myk%210t%233Catalog@vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443/mk_products  --output=http://elastic:ror1UItXjimUbans9Ph6@10.15.133.21:9200/mk_products    --type=settings


elasticdump --input=https://admin:Myk%210t%233Catalog@vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443/mk_products  --output=http://elastic:ror1UItXjimUbans9Ph6@10.15.133.21:9200/mk_products    --type=mapping

elasticdump --input=https://admin:Myk%210t%233Catalog@vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443/mk_products  --output=http://elastic:ror1UItXjimUbans9Ph6@10.15.133.21:9200/mk_products    --type=data

elasticdump --input=https://admin:Myk%210t%233Catalog@vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443/mk_products    --output=/home/vagrant/dump.json   --type=data 

nohup elasticdump --input=https://admin:Myk%210t%233Catalog@vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443/mk_products  --output=http://elastic:ror1UItXjimUbans9Ph6@10.15.133.21:9200/mk_products  --type=data --search='{"from": 0, "size": 50000, "query": { "term": { "retailerId": 265367 } } }' &


./bin/logstash -f logstash-input-opensearch.conf
nohup  ./bin/logstash -f logstash-input-opensearch.conf  &

curl -XGET http://10.15.133.21:9200/_cat/indices?v -u elastic:ror1UItXjimUbans9Ph6
curl -XGET 'https://vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com/_cat/indices' -u 'admin:Myk!0t#3Catalog'
curl -XGET http://localhost:9200/_cat/indices?v


# install nohup
dpkg -S /usr/bin/nohup
nohup  ./bin/logstash -f logstash-input-opensearch.conf  &

# stop nohup
ps -ef | grep sleep
kill -9 PID2


input {
  opensearch {
    hosts       => "https://vpc-mykiot-prod-ecatalog-100-es-5sls5rknjfrnctuj3sv7gpyjd4.ap-southeast-1.es.amazonaws.com:443"
    user        => "admin"
    password    => "Myk!0t#3Catalog"
    index       => "mk_products"
    query => '{ "query": { "terms": { "retailerId": [474614, xxxx] } } }'

  }
}

output {
  elasticsearch {
    hosts => "http://10.15.133.21:9200"
    user => "elastic"
    password => "ui"
    index => "mk_products"
  }
}