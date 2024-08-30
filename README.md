# AKS_Lab
Practice deploying the Datadog Agent to an AKS cluster.

__________

Change context to your AKS cluster, if necessary:

az account set --subscription <SUBSCRIPTION ID>
az aks get-credentials --resource-group <RG> --name <CLUSTER NAME> --overwrite-existing

Download repo, add it to helm, & install the operator:

--

git clone https://github.com/jgibbons-cp/datadog

helm repo add datadog https://helm.datadoghq.com

helm repo update

kubectl create ns datadog && kubectl config set-context --current --namespace=datadog

helm install dd-operator datadog/datadog-operator

--

If you get “Error while getting hostname” error, may need to add environment variable that specifies 

--

[OPTIONAL] Create Kubernetes secret, called “datadog-agent”:

kubectl create secret generic datadog-agent \
  --from-literal=api-key=<YOUR_DATADOG_API_KEY> \
  --from-literal=app-key=<YOUR_DATADOG_APP_KEY>

--

Create a kubernetes YAML manifest by copying Jenks’ example from his github: https://github.com/DataDog/datadog-operator/blob/main/examples/datadogagent/datadog-agent-all.yaml

--

nano datadog-agent-all.yaml

Copy/paste Jenks’ example into the new file, write out & exit

Follow the suggested flags in the repo:

Unless you are using a cluster other than minikube or kind, change the following to false:

ebpfCheck
cspm
cws
npm
usm

Unless you are using a cluster other than minikube, kind, AKS or kubeadm, add in the global section:

global:
  kubelet:
    tlsVerify: false

--

Apply the manifest via “kubectl apply -f datadog-agent-all.yaml”

Validate the setup with 'kubectl watch pods' and check your Datadog sandbox account!
