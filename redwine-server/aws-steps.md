# Create the Repository in ECR
aws ecr create-repository --repository-name redwine-repo 

# Create the Login and Password for the ECR Repository
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 113170463366.dkr.ecr.us-east-1.amazonaws.com

# Create the Login and Password for the ECR Repository
docker tag redwineflask:latest 113170463366.dkr.ecr.us-east-1.amazonaws.com/redwine-repo:v1

# Push the image into the  ECR Repository
docker push 113170463366.dkr.ecr.us-east-1.amazonaws.com/redwine-repo:v1

############################################################

# Create the EKS cluster
eksctl create cluster \
--name agredwine5 \
--region us-east-1 

# Update the kubeconfig
aws eks --region <region-code> update-kubeconfig --name <cluster_name>
aws eks --region us-east-1 update-kubeconfig --name agredwine4

# Run the YAML
kubectl apply -f redwine-eks-ingress.yaml

# Run the loadbalancer
export loadbalancer=$(kubectl get svc redwine-service -o jsonpath='{.status.loadBalancer.ingress[*].hostname}')
curl -m3 -v ${loadbalancer}
curl -L -v ${loadbalancer}
curl -k -s http://${loadbalancer} 
curl -vv ${loadbalancer}

# Delete the cluster
eksctl delete cluster --name <prod>
eksctl delete cluster --name agredwine4