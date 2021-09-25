# traefik-workshop-2
Advanced Load Balancing with Traefik 2.5

Agenda:

1. Deploying Traefik using the official Helm Chart
2. Deploying sample applications and exposing them using IngressRoute.
3. Weigheted Round Robin using Traefik Service and headers.
4. Mirroring.
5. Load balancing with sticky session
6. Nested healthchecks with file provider. 


## Deploying Traefik with the official Helm Chart

* Add Traefik's chart repository to Helm: 

```sh
helm repo add traefik https://helm.traefik.io/traefik
```

* Update the chart repository by running: 

```
helm repo update
```

* Customise the `values.yaml` by adding extra configuration. 

* Create Traefik namespace: 

```sh
kubectl create namespace traefik 
```

* Deploy Traefik by running the following command:

```sh
helm upgrade --install traefik -f values.yaml traefik/traefik -n traefik
```

* Verify the installation. 

* Explore Traefik dashboard by creating port forwarding to Traefik's pod by running the following command:

```
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000
```


## Deploying sample applications

* Create a namepsace where apps and services will be created
* Deploy the applications 