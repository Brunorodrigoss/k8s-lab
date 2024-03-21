![Alt text](/img/k8s-lab-notes.png?raw=true "K8s notes")

kubectl cluster-info --context kind-kind
kind create cluster --config=k8s/kind.yaml --name=fullcycle

kubectl cluster-info --context kind-fullcycle
kubectl config use-context kind-fullcycle

kubectl rollout history deployment goserver
kubectl rollout undo deployment goserver
kubectl rollout undo deployment goserver --to-revision=x

kubectl proxy --port=8080

Install =>
  kubectl: https://kubernetes.io/docs/tasks/tools/
  Local Tests: kind (Recommended) https://kind.sigs.k8s.io/docs/user/quick-start#installation



Kind:
  kind create cluster
  kubectl cluster-info --context kind-kind
  docker ps
  kubectl get nodes

  kind get clusters
  kind delete clusters kind 
  kind get clusters
  docker ps

  kind create cluster --config=k8s/kind.yaml --name=fullcycle
  kubectl cluster-info --context kind-fullcycle

  kubectl config get-clusters
  kubectl config use-context <CLUSTER NAME>


  kubectl apply -f k8s/pod.yaml

  kubectl port-forward pod/goserver 8000:80

  kubectl get pods
  kubectl delete pod goserver

  kubectl apply -f k8s/replicaset.yaml

  kubectl describe pod <POD NAME>

  kubectl get replicasets
  kubectl delete replicaset <REPLICASET NAME>

  kubectl get deployments

  kubectl rollout history deployment goserver
  kubectl rollout undo deployment goserver
  kubectl rollout undo deployment goserver --to-revision=1

  kubectl apply -f k8s/service.yaml
  kubectl get services
  kubectl port-forward service/goserver-service 8000:80

  kubectl proxy --port=8080

  kubectl apply -f k8s/configmap-myconfig.yaml
  kubectl get configmaps
  kubectl exec -it goserver-78b8fdf887-stm75 -- bash
  goserver-56bd7798dc-shx88

  kubectl apply -f k8s/secret.yaml
  http://localhost:9000/secret

  kubectl apply -f k8s/deployment.yaml && watch -n1 kubectl get pods

  HPA => Horizontal Pod Autoscaler
    - Install metrics-server (https://github.com/kubernetes-sigs/metrics-server)
      - wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
      - rename components to metrics-server
      - open the file and find by Deployment section
        - According to the issue (https://github.com/kubernetes-sigs/metrics-server/issues/525#issuecomment-746809546) we need to add an argument (spec containers args) to run the metrics-server using kind
          - --kubelet-insecure-tls

      After change the file we need to apply this configuration:
      kubectl apply -f k8s/metrics-server.yaml

      To check if the metrics-server was installed find the metrics server (v1beta1.metrics.k8s.io) after this command:
      
---

kubectl top pod

kubectl get hpa

---
https://github.com/fortio/fortio

kubectl run -it fortio --rm --image=fortio/fortio -- load -qps 800 -t 120s -c 70 "http://goserver-service:8080/healthz"

---
kubectl get storageclasses
kubectl config get-contexts
kubectl apply -f k8s/pvc.yaml 
kubectl get pvc

---
Stateless (Is not necessary to store the information)
Stateful (The information need to be stored) Eg. Database

kubectl scale statefulset mysql --replicas=10
ping mysql-3.mysql-h

--
nginx ingress: https://kubernetes.github.io/ingress-nginx/deploy/
Install ingress-nginx using helm:
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace

kubectl get svc --namespace ingress-nginx
kubectl get pods --namespace ingress-nginx


kubectl get namespaces
---
Cert Manager (TLS): https://cert-manager.io/
https://cert-manager.io/docs/installation/kubectl/

kubectl get service --namespace cert-manager

Create cert issuer
Change Ingress

kubectl get certificates

----
Namespaces:
kubectl get namespaces
kubectl get pods -n kube-system
kubectl create namespace dev
kubectl create namespace prod
kubectl get pods -l app=server
kubectl config view
kubectl config current-context
kubectl config set-context dev --namespace=dev --cluster=kind-fullcycle --user=kind-fullcycle
kubectl config set-context prod --namespace=prod --cluster=kind-fullcycle --user=kind-fullcycle
kubectl config use-context dev

---
Service Accounts:
kubectl get serviceaccounts
/var/run/secrets/kubernetes.io/serviceaccount

kubectl api-resources

