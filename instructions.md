**[Link to files](https://drive.google.com/open?id=1QtqdRwyotfdwzix5F14pKPxRCwHwFVOM) **

**Canary**


```
kubectl get pods

cat frontend-destination.yaml
kubectl apply -f frontend-destination.yaml

cat route-rule-frontend-100.yaml
kubectl apply -f route-rule-frontend-100.yaml

kubectl apply -f frontend-blue.yaml
kubectl get pods


cat route-rule-frontend-80-20.yaml

kubectl apply -f route-rule-frontend-80-20.yaml

#Setup for API demo:
#required steps to setup the environment for the api section of the demo

kubectl apply -f route-rule-frontend-default.yaml
kubectl delete -f frontend-blue.yaml
kubectl delete -f frontend-destination.yaml
```




**API Management demo**


```
kubectl apply -f route-rule-frontend-default-api.yaml

#the API demo breaks the frontend UX
#set the ingress ip
export GATEWAY=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

#display ingress ip 
echo $GATEWAY

#call without api key
curl $GATEWAY/tracking/123 

#protect it
kubectl apply -f rule.yaml

#define apigee attributes for analytics, authorization
vi definitions.yaml

#define how to connect to apigee instance
vi handler.yaml

#define when to apply apigee rules
vi rule.yaml

#call again without api key, should fail
curl $GATEWAY/tracking/123

## add portal instructions here

# call with api key
curl $GATEWAY/tracking/123 -H "x-api-key: gNsHkTu2jCjYG9GjAg46ihF5Wc0PkuTf" -v

#switching to Oauth
kubectl apply -f shipping-auth.yaml
vi shipping-auth.yaml

# Generate Token
export TOKEN=$(./apigee-istio token create -o amer-demo13 -e test -i gNsHkTu2jCjYG9GjAg46ihF5Wc0PkuTf -s VgwEAdGUDpOt4DgN)

#show token
echo $TOKEN

#verify token
echo $TOKEN > token.txt
./apigee-istio token inspect -f token.txt -o amer-demo13 -e test

#call with token
curl $GATEWAY/tracking/123 -H "Authorization: Bearer $TOKEN"

#show analytics ui in Apigee
https://apigee.com/edge

#go to org → amer-demo13
#environment == test 
#show ANALYZE → proxy-performance
https://apigee.com/platform/amer-demo13/proxy-performance

#show ANALYZE → developer-engagement
https://apigee.com/platform/amer-demo13/developer-engagement
```




**Canary clean up**


```
kubectl apply -f route-rule-frontend-default.yaml
kubectl delete -f frontend-blue.yaml
kubectl delete -f frontend-destination.yaml
```


**API Management demo clean up**


```
kubectl delete -f shipping-auth.yaml
kubectl delete -f rule.yaml

#Leave running
#kubectl delete -f shipping-api.yaml
#kubectl delete -f definitions.yaml
#kubectl delete -f handler.yaml


