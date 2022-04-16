#gke_deploy_service

artifactregistry.googleapis.com

export PROJECT_ID=PROJECT_ID

gcloud artifacts locations list

export REGION=us-central1

gcloud config set project $PROJECT_ID 

gcloud artifacts repositories create hello-repo \
   --repository-format=docker \
   --location=$REGION \
   --description="Docker repository"

 gcloud artifacts repositories add-iam-policy-binding hello-repo --member=serviceAccount:<service-account-name>@<project-name>.iam.gserviceaccount.com --role='roles/editor'

 gcloud artifacts repositories add-iam-policy-binding hello-repo --member=serviceAccount:iac-gke@makinap0001.iam.gserviceaccount.com --role='roles/editor' --location=$REGION

 

git clone https://github.com/GoogleCloudPlatform/kubernetes-engine-samples
cd kubernetes-engine-samples/hello-app

docker build -t $REGION-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1 .

gcloud auth configure-docker REGION-docker.pkg.dev

docker push $REGION-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1

docker run --rm -p 8080:8080 $REGION-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1

gcloud auth configure-docker $REGION-docker.pkg.dev

kubectl get nodes

gcloud container clusters get-credentials gke-cluster --zone $REGION

cat ~/.kube/config

kubectl create deployment hello-app --image=$REGION-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1

kubectl scale deployment hello-app --replicas=3

kubectl autoscale deployment hello-app --cpu-percent=80 --min=1 --max=5


kubectl expose deployment hello-app --name=hello-app-service --type=LoadBalancer --port 80 --target-port 8080

kubectl get service








