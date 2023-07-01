Get all the nodes
kubectl get nodes
kubectl get no
kubectl get node
kubectl get nodes -o wide

Get the output in yaml
kubectl get nodes -o yaml

Get all the pods
kubectl get pods
kubectl get pods --all-namespaces

Describe nodes
kubectl describe node/docker-desktop

kubectl api-resources

Explain Nodes
kubectl explain nodes
kubectl explain node.spec
kubectl explain node --recursive

kubectl get services
kubectl get svc

Namespaces
kubectl get namespaces
kubectl get pods --namespace=kube-system
kubectl get pods -n kube-system
kubectl delete
kubectl label

Securtiy bootstrapping
kubectl -n kube-public get pods
kube-public-> bootstrapping

kubectl -n kube-public get configmaps
kubectl -n kube-public get configmap cluster-info -o yaml


Running our container
kubectl run pingpong --image alpine ping 1.1.1.1

1.18 Changes to Kubectl Run
Creating Pods vs. Deployments in 1.18+
Starting in version 1.18 (released March 2020), the kubectl run command only does one thing: create single pods. There were many reasons for this, but the big ones were to reduce the complexity of how the run command worked and to move other resource creation to the kubectl create command. The idea is that kubectl run is now similar to docker run. It creates a single pod, where docker run creates a single container.

Now that everyone's finally using newer versions like 1.24+ I'm replacing these videos. You can follow along on my course updates GitHub project: https://bret.show/courseupdates

Depending on which version of Kubernetes you have installed, you'll need to decide how you'll create objects. Here's a cheat sheet for how old commands should be used with the 1.18+ changes.

Create a single pod in 1.18+
kubectl run nginx --image nginx

Create a single pod with a custom command in 1.18+
kubectl run pingpong --image alpine --command -- ping 1.1.1.1

Create a deployment in 1.18+
kubectl create deployment nginx --image nginx

Create a deployment with a custom command in 1.18+
 kubectl create deployment pingpong --image alpine -- ping 1.1.1.1

Notice the double dash -- which separates the kubectl command options from the COMMAND you want to override when starting the containers.

Also notice that run requires the --command -- <cmd> <arg> while the create deployment just needs -- <cmd> <arg> at the end.

For more info on the create deployment options, check out the help at kubectl create deployment --help


Kubectl logs
kubectl logs deploy/nginx
It will work only for deployed apps
for deployment use
kubectl create deployment <name> --image <image-name>
kubectl create deployment nginx --image nginx


Log multiple containers
kubectl logs -l run=pingpong2 --tail 1 -f

Scale containers
kubectl scale deployment pingpong2 --replicas=8

Limit to 5 containers only







