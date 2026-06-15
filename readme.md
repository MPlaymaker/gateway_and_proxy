## Steps:

1. Install Docker Desktop, Enable Kubernetes, Create cluster
2. Create **apps.yaml** - This describes the NumberService, StringService and their deployment configuration
3. Create **gateway.yaml** - This describes the API gateway pod configuration (service and deployment)
	We use Envoy as API gateway here
4. Create **rp_configmaps.yaml** - This defines the config maps for ReverseProxy (service, SSI - Server Side Includes, configuration)
	We use NGINX as reverse Proxy here
5. Create **rp_deployment.yaml** - This defines the Reverse proxy deployment

In Terminal run following command

kubectl apply -f apps.yaml \
kubectl apply -f gateway.yaml \
kubectl apply -f rp_configmaps.yaml \
kubectl apply -f rp_deployment.yaml \
kubectl rollout restart deployment/numberservice-deployment \
kubectl rollout restart deployment/stringservice-deployment \
kubectl rollout restart deployment/api-gateway-deployment \
kubectl rollout restart deployment/reverse-proxy-deployment


## What just occurred under the hood?

Your terminal command fired an HTTP request to localhost:80.

Docker Desktop routed that traffic right into your NGINX Reverse Proxy Service.

The Reverse Proxy evaluated the template and discovered it needed to fetch internal components.

It sent two simultaneous internal requests over to the Envoy API Gateway.

The API Gateway checked the prefixes (/number and /string), stripped them down, and safely distributed 
the tasks out to the Python Microservice Pods.

The Python engines returned the plain-text string payloads back to the Gateway, which passed them to NGINX,
which neatly assembled them and dropped them into your terminal window.



