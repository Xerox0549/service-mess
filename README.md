# Into the service mess there are a specific tool popular tool for manage all the services into service mess called istio 

1. How to install istio into kind cluster and run also create a dir for seperate istio 

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.28.0
ls
cat README.md
kind create cluster --name istio-testing
```
2. Install istio 
```bash
istioctl install -f samples/bookinfo/demo-profile-no-gateways.yaml -y
```
3. Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later:
```bash
kubectl label namespace default istio-injection=enabled
```
4. Install the Kubernetes Gateway API CRDs it is used for read write custom resources that is not present into kubernetes for there development 
```bash
kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
{ kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.4.0" | kubectl apply -f -; }
```
4. Deploy the sample application this is a sample application for check the istio is work or not properly
```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```
5. Now time to check the things what i do 
```bash
kubectl get all 
```
6. If we neeed to open indivial to each other as like service product page and open product page browser but still it is so complicated to manage all the service as same like product page reviews and rating also as same time to give permissin to all thats why we use istio for all the things
```bash
kubectl get svc
kubectl port-farwording svc/productpage 9080:9080 
```
7. Validate that the app is running inside the cluster by checking for the page title in the response:
```bash
kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
# its show <simple book store sample>
```
8. Open the application to outside traffic (this directly meaning thats give the access permisson for outer side using using gateway)
```bash
kubectl apply -f samples/bookinfo/gateway-api/bookinfo-gateway.yaml
# gateway.gateway.networking.k8s.io/bookinfo-gateway created
# httproute.gateway.networking.k8s.io/bookinfo created
# the gateway is visible or not
kubectl get gateway
```
9. open the gateway for the user access at the end we can see the result 
```bash
kubectl port-forward svc/bookinfo-gateway-istio 8080:80
```
10. its show page can't be found this is the why becouse it run using /productpage after 8080
11. if we need to run it as background only & 
```bash
kubectl port-forward svc/bookinfo-gateway-istio 8080:80 &
```
12. Here if we need to access the kiali dashboard before that we need to give traffic to my webeapplication
```bash
for i in $(seq 1 100); do curl -s -o /dev/null "http://changeitforlocalhost/productpage"; done
```
13. Now time to check the kiali dashboard 
```bash
kubectl apply -f samples/addons/kiali.yaml
kubectl rollout status deployment/kiali -n istio-system
# Waiting for deployment "kiali" rollout to finish: 0 of 1 updated replicas are available...
#deployment "kiali" successfully rolled out

```
14. command for kiali dashboard here we can show all 
```bash
istioctl dashboard kiali
for i in $(seq 1 100); do curl -s -o /dev/null "http://changeitforlocalhost/productpage"; done
# this is give the request for 100 times as we see the traffic of website
```



