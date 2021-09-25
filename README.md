# Advanced Load Balancing with Traefik 2.5
## traefik-workshop-2

Agenda:

1. Deploying Traefik using the official Helm Chart
2. Deploying sample applications and exposing them using IngressRoute.
3. Weighted Round Robin using Traefik Service and headers.
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
* Create Ingressroute for deployed applications

## Weigthed Round Robin - Canary Deployment with Traefik

* Deploy TraefikService and set the weights for each of the service
* Deploy Ingressroute that use TraefikService, see the response comes from different services based on defined weights in the TraeikService
* 
* Deploy custom header 
* Deploy Ingressroue with enhanced matching rule by Custom HTTP Header
* Validate the deployment by sending HTTP request with added extra HTTP header
```sh
curl https://v.waw.demo.traefiklabs.tech -H "X-Canary-Header: knock-knock"
```
## Mirroring

* Deploy Traefik Service that introduces mirroring
* Create Ingressroute that use the created TraefikService
* Verify your configuration, see the logs

## Sticky Session

* Deploy Traefik service with Sticky Session configuration
* Deploy Ingressroute that points to the sticky session Traefik Service
* Test your configuration using `curl -vv` or examine the cookies using Developer Tools.

## Nested Healtchecks

The feature is currently only available as dynamic configuration provided through File. That's why we have to add file provider that points to a config map containing Traefik's extra configuration

* Create configmap:

```sh
 kubectl create configmap traefik-configs -n traefik --from-file=dynamic.yaml=config/dynamic.yaml 
```

* Add File provider and VolumeMounts to the `values.yaml`
* Deploy Traefik using the files `values.yaml`
```sh
helm upgrade --install traefik -f values.yaml traefik/traefik -n traefik
```
* Verify running configuration using the following command:

```sh
curl https://nested.waw.demo.traefiklabs.tech
```

You should receive responses from both services whoamiv1 and whoamiv2 according to the weighets set in the dynamic configuration. 

* Scale down to 0 replicas on one of the service
 
```sh
kubectl scale deployment whoamiv2 --replicas=0
```

* Verify configuration using the following command:

```sh
curl https://nested.waw.demo.traefiklabs.tech
```

You should receive reposnes only from `whoamiv1`.

Traefik automatically discover that `whoamiv2` is down and send reuqests only to the healthy services, that is `whoamiv1`.
