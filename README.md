# Prometheus & grafana

## Install with homebrew & helm

Prerequisites:
Install Docker Desktop (for Mac) if not already installed.
Install Minikube by following the official Minikube installation guide.
Minikube typically comes with kubectl, but if needed, install the latest version of kubectl 

minikube start --driver=docker
minikube addons enable metrics-server
brew install helm
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace

kubectl get pods -n monitoring
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090

get admin password for grafana:

kubectl get secret -n monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

kubectl port-forward -n monitoring svc/prometheus-grafana 3000:80

helm uninstall prometheus -n monitoring
kubectl delete namespace monitoring




kubectl create -f prometheus-operator-crd


kubectl apply -R -f monitoring 

kubectl -n monitoring port-forward svc/prometheus-operated 9090
kubectl -n grafana port-forward svc/grafana 3000



#####
# Thanos 
## Multi cluster Prometheus

