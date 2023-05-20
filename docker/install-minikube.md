curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

apt-cache policy docker-ce

sudo apt install docker-ce  -y

docker run --rm -it --rm -v /var/run/docker.sock:/var/run/docker.sock --network=host --workdir /root brakmic/devops:latest
 ./create_cluster.sh config.yml 

kubectl get po -A 


docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock -v ${PWD}:/root/local --rm --network=host --workdir /root brakmic/devops:latest

create_cluster.sh
