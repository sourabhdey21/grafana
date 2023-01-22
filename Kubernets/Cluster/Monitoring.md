

## Install docker & containerd in the system to create the kubernetes cluster

`sudo yum install -y yum-utils`

 `sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo`
    
 `sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin`
 
 ## Start the docker engine

`systemctl start docker`

## Check the status
![image](https://user-images.githubusercontent.com/98477908/213910555-100dda09-6581-49d1-b5f5-dcc8316a2453.png)

 
 ## Install kind or minikube to create local kubernetes cluster in your enviroment
 
`curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.17.0/kind-linux-amd64`

`chmod +x ./kind`

`sudo mv ./kind /usr/local/bin/kind`


## Initialize the kubernetes cluster  i will named it grafana for testing purpose 

`kind create cluster --name grafana`



## To talk to your kubernetes cluster you have install kubectl 

`curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"`

`chmod u+x kubectl`

`mv kubectl  /usr/local/bin/`

![image](https://user-images.githubusercontent.com/98477908/213911015-b33aa514-9244-437a-9d32-929c87f6d172.png)

## Verify the kubernetes cluster 


`kubectl   cluster-info kind-grafana`

![image](https://user-images.githubusercontent.com/98477908/213911132-c183542d-63dc-4c25-8542-e949eea16cc3.png)

![image](https://user-images.githubusercontent.com/98477908/213911135-20c82c50-1d9b-4d42-9462-a65b02a6747b.png)


# Install helm

`mkdir helm`

`cd helm/`

`wget https://get.helm.sh/helm-v3.7.0-linux-amd64.tar.gz`

`tar xvf helm-v3.7.0-linux-amd64.tar.gz`

`sudo mv linux-amd64/helm /usr/local/bin`

## Check the helm version

helm version

![image](https://user-images.githubusercontent.com/98477908/213911422-0f43ed73-37b1-479c-9a85-d2e1dfe88fed.png)


## add prometheus Helm repo 

`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`

## add grafana Helm repo
`helm repo add grafana https://grafana.github.io/helm-charts`

## Output

![image](https://user-images.githubusercontent.com/98477908/213911561-3acb832d-bd2b-4b2c-9e4b-af3a31ac1984.png)



## Install promeheus in your cluster

`kubectl create ns prometheus`

`helm install prometheus prometheus-community/prometheus --namespace prometheus  --set server.storageClass=local-storage`


# Output

![image](https://user-images.githubusercontent.com/98477908/213912625-b1e1484f-de8d-4250-8fd7-dfe3cad0fc51.png)



#3 Verify the pods 

` kubectl get pod -n prometheus `

![image](https://user-images.githubusercontent.com/98477908/213912697-dfd5b082-4c6d-4d29-8e94-32c6e248997c.png)



![image](https://user-images.githubusercontent.com/98477908/213913325-5eca2b0c-0510-40c0-801c-6fdf3dae1cb7.png)



## Install grafana 

`kubectl  create ns grafana`

vim grafana.yml

![image](https://user-images.githubusercontent.com/98477908/213913766-9fcd4a8e-5918-4cdc-8cf3-4765db47ffba.png)

![image](https://user-images.githubusercontent.com/98477908/213913812-544f0085-cdd7-4c3e-b50d-1e5b3ff76684.png)



## Now expose the promethus and grafana to access by your client 


## expose the promethus service 

`kubectl port-forward -n prometheus deploy/prometheus-server 8080:9090 --address='0.0.0.0'`


![image](https://user-images.githubusercontent.com/98477908/213913964-28739a89-544b-48bb-b192-73d2d7dad11c.png)




## expose the grafana service for visualize   
