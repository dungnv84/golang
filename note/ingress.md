helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm show values ingress-nginx/ingress-nginx > values.yaml


helm install nginx-ingress-controller ingress-nginx/ingress-nginx -f values.yaml -n   ingress-nginx