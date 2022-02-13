1. Create EKS Cluster
    $ eksctl create cluster -f k8s-cluster.yaml 
2. Configure kubeconfig to your eks cluster
    $ aws eks --region eu-west-1 udpate-kubeconfig --name igor-eks-demo
3. Check your nodes status
    $ kubectl get node -L "failure-domain.beta.kubernetes.io/zone,node-lifecycle"
4. Kubectl Deploy Kubernetes Dashboard UI
    $ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml
5. Log in with Token and port-forwarding
    $ k port-forward svc/kubernetes-dashboard -n kubernetes-dashboard 8082:443
    $ aws eks get-token --cluster-name igor-eks-demo | jq -r '.status.token'
6. Deploy cluster autoscaler
    https://docs.aws.amazon.com/eks/latest/userguide/cluster-autoscaler.html
    k apply -f ./cluster-autoscaler/*
    k get cm -n kube-system -o yaml cluster-autoscaler-status
7. Deploy K8s Metrics Server
   $ kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
8. Helm
   Install Helm cli - https://helm.sh/docs/intro/install/
   helm repo add stable https://charts.helm.sh/stable
   helm repo update
9. Install Kube-ops-view on the cluster
https://www.eksworkshop.com/beginner/080_scaling/
$ helm install kube-ops-view stable/kube-ops-view --set service.type=LoadBalancer --set rbac.create=True
$ helm list
10. Deploy HPA Demo
For cluster autoscaler use:
First show manual scaling
kubectl create deployment php-apache --image=us.gcr.io/k8s-artifacts-prod/hpa-example
kubectl set resources deploy php-apache --requests=cpu=200m
kubectl expose deploy php-apache --port 80
kubectl get pod -l app=php-apache
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
kubectl --generator=run-pod/v1 run -i --tty load-generator --image=busybox /bin/sh
while true; do wget -q -O - http://php-apache; done
https://www.eksworkshop.com/beginner/080_scaling/test_hpa/
! Delete pods and check how they are rescheduled on another node
11. Rolling Update 
Create 'curl-test.yaml' and webapp.yaml  - Observe
Update the version to v2 while running 'curl-test.sh' script - Observe
Change the deployment type from rolling update to Recreate while running 'curl-test.sh' script - Observe
12. Show liveness probe demo
k apply -f Healthchecks/liveness-app.yaml
kubectl exec -it liveness-app -- /bin/kill -s SIGUSR1 1
https://www.eksworkshop.com/beginner/070_healthchecks/livenessprobe/
13. Create ALB Ingress
https://www.eksworkshop.com/beginner/180_fargate/prerequisites-for-alb/
14. Deploy on Fargate Demo
$ kubectl apply -f fargate/2048_full.yaml
$ kubectl -n demo rollout status deployment deployment-2048
$ kubectl get ingress/ingress-2048 -n demo
15. Deploy another customer on Fargate Demo 
$ kubectl apply -f fargate/2048_full-2.yaml
16. Rolling update Demo
18. "Test"


