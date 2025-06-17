# Deploy Container Using Azure Container Registry (ACR)

This guide explains how to create an Azure Container Registry (ACR), build and push a Docker image to it, and then deploy a container instance using that image.

## Prerequisites

Azure CLI installed or use Azure Cloud Shell  
Docker installed and running on your local system  
Azure account and subscription  
Logged in via Azure CLI using az login

## Step 1: Create a Resource Group

az group create --name VaishsResourceGroup --location centralindia

## Step 2: Create an Azure Container Registry

az acr create --resource-group VaishsResourceGroup --name vaishacrregistryvaishnavi01 --sku Basic

## Step 3: Login to ACR

az acr login --name vaishacrregistryvaishnavi01

## Step 4: Create Docker Application

mkdir MyDockerApp  
cd MyDockerApp

Create a Python file

echo 'print("Hello from Docker!")' > app.py

Create a Dockerfile and paste the following

FROM python:3.9-slim  
WORKDIR /app  
COPY . .  
CMD ["python", "app.py"]

Save the above content as a file named Dockerfile (no extension)

## Step 5: Build Docker Image

docker build -t vaishacrregistryvaishnavi01.azurecr.io/vaishapp:v1 .

## Step 6: Push Image to Azure Container Registry

docker push vaishacrregistryvaishnavi01.azurecr.io/vaishapp:v1

## Step 7: Retrieve ACR Credentials

az acr credential show --name vaishacrregistryvaishnavi01

Note the username and password from the output for the next step

## Step 8: Create a Container Instance from ACR Image

az container create --resource-group VaishsResourceGroup --name vaishappcontainer --image vaishacrregistryvaishnavi01.azurecr.io/vaishapp:v1 --cpu 1 --memory 1 --registry-login-server vaishacrregistryvaishnavi01.azurecr.io --registry-username <username> --registry-password <password> --dns-name-label vaishappcontainer --ports 80

Replace <username> and <password> with the actual values from previous step

## Step 9: Verify the Container

To list container instances

az container list --resource-group VaishsResourceGroup --output table

To describe specific container

az container show --resource-group VaishsResourceGroup --name vaishappcontainer --output json

To access the application

http://vaishappcontainer.centralindia.azurecontainer.io

## Step 10: Verify Image in ACR

List repositories

az acr repository list --name vaishacrregistryvaishnavi01 --output table

List tags

az acr repository show-tags --name vaishacrregistryvaishnavi01 --repository vaishapp --output table

## Cleanup (Optional)

az container delete --name vaishappcontainer --resource-group VaishsResourceGroup --yes  
az group delete --name VaishsResourceGroup --yes --no-wait
