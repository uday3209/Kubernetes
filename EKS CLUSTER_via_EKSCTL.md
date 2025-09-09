# Steps to Host EKS CLUSTER via EKSCTL 
## <br> Create IAM role/user and attach following policies
 ECRfull access <br>
 EKSpolicy <br>
 IAM full access

 ## Install AWS CLI and configure with IAM user Credentials

 ##### Note: If ubuntu os
```
  apt update
  apt install unzip
```
```
 curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
 unzip awscliv2.zip
 sudo ./aws/install
 aws configure
```
 
 ## Install EKS Tool
```
 curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
 sudo mv /tmp/eksctl /usr/local/bin
 eksctl version
```

 ## Install Kubectl
 ```
 curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
 sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl 
 kubectl version --client
 ```

### Generate keys fir cluster creation
 ```
 ssh-keygen
 ```
 
 ## Create EKS Cluster ( Edit Your custom Cluster Name )
  ### Method-1
 ```
 eksctl create cluster --name my-cluster --region us-east-1 --version 1.32 --node-type t2.micro --nodes 3 --nodes-min 2 --nodes-max 4 --ssh-access --ssh-public-key /root/.ssh/id_rsa.pub
```
 ### Method-2
 ```
 eksctl create cluster --name my-cluster --region region-code --version 1.32 --vpc-public-subnets subnet-ExampleID1,subnet-ExampleID2 --without-nodegroup

 eksctl create nodegroup \
  --cluster my-cluster \
  --region us-east-2 \
  --name my-node-group \
  --node-ami-family Ubuntu2204 \
  --node-type t2.micro \
  --subnet-ids subnet-086ced1a84c94a342,subnet-01695faa5e0e61d97 \
  --nodes 3 \
  --nodes-min 2 \
  --nodes-max 4 \
  --ssh-access \
  --ssh-public-key /root/.ssh/id_rsa.pub
```


 ### To Delete cluster
```
 eksctl delete cluster --name my-cluster
```



#### deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

#### service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```
