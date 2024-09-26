# Thanos 
## Multi cluster Prometheus


Remote Write method : latency is much more small

- Thanos components

Multi cloud setup with centralized thanos in one cloud and local prometheus instances in another cloud, next we start with remote read implementation and then we move to remote write implementation.

- we start with kubernetes

minikube start --kubernetes-version=1.30.0 --driver=docker
kubectl get nodes



 - Delete All Pods in Multiple Namespaces Using a Loop
for ns in monitoring grafana cadvisor ingress-nginx; do kubectl delete pods --all -n $ns; done


kubectl apply -f monitoring-ns.yaml
create custom resource defination : 
kubectl create -f prometheus-operator-crds
kubectl apply -R -f prometheus-operator

kubectl get pods -n monitoring
 kubectl get pods -n monitoring --show-labels

 - now we can use name labels to retrieve logs
   

   kubectl logs -l app.kubernetes.io/name=prometheus-operator -n monitoring -f

   - if there is mis configuration you might see permission errors in the operator logs


kubectl apply -f prometheus
watch -t kubectl get pods -n monitoring
kubectl logs -l app.kubernetes.io/name=prometheus-operator -n monitoring -f

kubectl port-forward svc/prometheus-operated 9090 -n monitoring

for storage bucket that keeps the logs metrics 
we use minio, open source object-store ( is cheaper than S3), 
for data processing pipelines, hadoop alternative

kubectl apply -f minio-ns.yaml
kubectl apply -f minio
kubectl get pods -n minio
kubectl get svc -n minio 

with Mimio user interface, you cant use port forwarding beacuse they implemented websockets,
so we can usr ingress or minikube shortcut for local testing.


minikube service minio-console --url -n minio

we create a bucket and access key

first is deploying sidecar along side Prometheus.

we can use the prometheus operator and costum resource for this, we create secret objet first with the name of objectstore
deploy a sidecar inside prometheus configuration
reapply prometheus object: kubectl apply -f prometheus
the third contaner will be thanos sidecar
check the logs pf thanos with -c 
 kubectl logs -f prometheus-staging-0 -c thanos-sidecar -f -n monitoring


The main use case for the sidecar is to enable querying the local prometheus from centralized thanos

then create a sidecar-svc

redeploy prometheus object

kubectl get svc -n monitoring

kubectl get endpoints -n monitoring