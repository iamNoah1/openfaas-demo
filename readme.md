# OpenFaaS Demo

## Prerequisites
* Helm installed
* Faas CLI installed 
* Docker installed

## Installation on existing Kubernetes
* `helm repo add openfaas https://openfaas.github.io/faas-netes/`
* `helm repo update `
* `kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml`
* `kubectl -n openfaas create secret generic basic-auth --from-literal=basic-auth-user=admin --from-literal=basic-auth-password="<pwd>"`
* `helm upgrade openfaas --install openfaas/openfaas --namespace openfaas --set basic_auth=true --set functionNamespace=openfaas-fn --set serviceType=LoadBalancer`
* `kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"` to check if installation was successful
* Note that are several other methods to get OpenFaaS up and running. 

## Create a new Function
* `dotnet new webapi -n dotnet-api --no-https` for creating a new dotnet based web API
* Create a Dockerfile for the new project (inside root of dotnet-api)
* `faas-cli new --lang dockerfile dotnet-api-func` 
* Adjust the created `stack.yml`
  * handler should point to the projec dir 
  * image defines specifies the full image name with version. Note that you need to think about in which registry space it will live. 

## Deploy Function 
* `faas-cli build` Try to build
* `docker run --rm -p 8080:80 -ti iamnoah4real/dotnet-api-func` to test if the image works as expected
* `export OPENFAAS_URL=""` Set the URL to the OpenFaaS installation. In our case we need to grab the public IP of the external gateway (`kubectl get svc gateway-external -n openfaas`). Note, that you need to add the `http://` as well as the port (8080)
* `docker login`
* `faas-cli login`
* `faas-cli push`
* `faas-cli deploy`
* Note, that `faas-cli up` combines `faas-cli build`, `faas-cli push` and `faas-cli deploy`

## Additional information
* https://github.com/alexellis/arkade 
* https://docs.microsoft.com/en-us/azure/aks/openfaas 
* https://docs.openfaas.com
* https://docs.openfaas.com/deployment/kubernetes/
* https://docs.microsoft.com/en-us/azure/aks/openfaas 