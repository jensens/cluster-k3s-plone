# Plone Cluster Configuration

## Install a cluster

I did this with Vagrant and Ansible. The code and instructions I wrote can be found at https://github.com/jensens/cluster-k3s

Do not do this on old hardware and not with with less than 16GB of memory. The cluster needs at least 8GB of memory.

After this, you have a running Kubernetes cluster with K3s and Traefik ingress.

Check if you can access Portainer.


## Preparation

We need a namespace and a Persistent Volume Claim.

```shell
kubectl create namespace demo
kubectl apply -n demo -f db-pvc.yaml
```

## Install Helm chart

Ensure you do not have any service blocking localhost port 3000 and 8080. Stop them otherwise to free the ports.

Helm charts are not released yet. In my case the Helm chart is git cloned in my workspace coredev folder. Adapt the path to your needs.

```shell
helm upgrade --namespace demo --install plone --values values.yaml ~/ws/cdev/helm-charts/plone6-volto-pg-nginx-varnish/
```

## Access the site

In Portainer check if there is all up and running. alternativly use kubectl to check it.

Since we have no site setup implemented yet, the site need to be added manually.

Since we did not configure any ingress, we need to use port-forwarding to access the sites backend.

```shell
kubectl port-forward -n demo service/plone-backend 8080
```

Then go to http://localhost:8080 and add a Plone site (first button). Keep the default settings.

Now we can access the nginx to see the Volto frontend.

```shell
kubectl port-forward -n demo service/plone-nginx 8081
```
Then go to http://localhost:8081 and you should see the Volto frontend.

