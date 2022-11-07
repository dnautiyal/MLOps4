<p align = "center" draggable=”false”
   ><img src="https://user-images.githubusercontent.com/37101144/161836199-fdb0219d-0361-4988-bf26-48b0fad160a3.png"
     width="200px"
     height="auto"/>
</p>

# <h1 align="center" id="heading">Week 8 - Deploying Pet-Bokeh using Kubernetes with Nvidia Triton on EC2</h1>

## 📚 Learning Objectives

By the end of this session, you will be able to:

- Create and configure a minikube cluster
- Integrate minikube with Nvidia Trition Server
- Deploy a minikube cluster on EC2

## 📦 Deliverables

- A screenshot of `docker ps`
- A screenshot of `minikube dashboard`
- A screenshot of your deployment


## Deployment on EC2

### Create EC2 Instance

- Go to EC2 console: <https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1>
- Create EC2 instance
- Pick deep learning ami
- Pick instance type: At least p3.2xlarge
- Create key-pair
- Download key
- Edit network
- Enable IPV4 address
- Open ports 8000-8004 from anywhere
- Launch Instance

### Install dependencies

- Get the ip address of the instance
- Change key permissions to 400 (`chmod 400 key.pem`)
- SSH into the machine `ssh -i key.pem ec2-user@ec2.ip.address`
- Install git if needed (`sudo apt install git` for ubuntu based distros, `sudo yum install git` for amazon linux)
- Install Docker (`sudo apt install docker` for ubuntu based distros, `sudo yum install docker` for amazon linux)
- Start Docker (`sudo systemctl start docker`)
- Add user to docker group (`sudo usermod -aG docker ${USER}`)
- Logout and Login again through SSH to take the group changes into account
- Check if docker installed correctly (`docker run hello-world`)
- Install minikube

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

```

- Start a minikube cluster (`minikube start`)
- Set an alias for kubectl (`alias kubectl="minikube kubectl --"`)
- For triton we need to add the credentials as secrets

```
kubectl create secret generic aws-env --from-literal='AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY' --from-literal='AWS_SECRET_ACCESS_KEY=YOUR_SECRET_ACCESS_KEY' --from-literal='AWS_DEFAULT_REGION=us-east-1'
```

### Locally built images

- Use minikube's docker (`eval $(minikube docker-env)`)
- Pull the triton image (`docker pull nvcr.io/nvidia/tritonserver:22.06-py3`)
- Build all the docker images

```
docker build -t main .
docker build -t face-emotion face-emotion/
docker build -t pet-bokeh pet-bokeh/
```

- Load all the kubernetes resources (`kubectl apply -f K8s/`)
- Forward the main port (`kubectl port-forward svc/main 8004:8004 --address 0.0.0.0`)

### Pulling from ECR

- Configure credentials (`minikube addons configure registry-creds`)
- Enable the addon (`minikube addons enable registry-creds`)
- Edit K8s/ECR/*.yaml to use your ECRs
- now run the following command to push the docker images to ECR
    - docker tag pet-bokeh:latest public.ecr.aws/d4e8a7g0/deepak-pet-bokeh/pet-bokeh:latest
    - docker push public.ecr.aws/d4e8a7g0/deepak-pet-bokeh/pet-bokeh:latest
    - docker tag face-emotion:latest public.ecr.aws/d4e8a7g0/deepak-face-emotion:latest
    - docker push public.ecr.aws/d4e8a7g0/deepak-face-emotion:latest
    - docker tag main:latest public.ecr.aws/d4e8a7g0/deepak-main:latest
    - docker push public.ecr.aws/d4e8a7g0/deepak-main:latest

- Load all the kubernetes resources (`kubectl apply -f K8s/`)
- Forward the main port (`kubectl port-forward svc/main 8004:8004 --address 0.0.0.0`)

### Start a Kubernetes Dashboard
- minikube dashboard --url
- kubectl proxy --address='0.0.0.0' --disable-filter=true --port 8001
- then whatever url you get change the ip to your ec2 box public ip, and port to 8000
  - for example http://54.89.188.194:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/


