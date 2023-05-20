- config env 
export KUBECONFIG=/home/dungnv8/.kube/dev-config:/home/dungnv8/.kube/staging-config:/home/dungnv8/.kube/new-prod-config:/home/dungnv8/.kube/prod-v1-config
- unset env
unset KUBECONFIG
- kubectx


export KUBECONFIG=/home/dungnv8/.kube/prod-v1-config

