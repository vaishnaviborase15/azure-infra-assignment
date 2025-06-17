# Deploy Docker App on Azure Container Instance with Container Groups

This guide explains how to deploy a simple Docker application using Azure Container Instances (ACI) and how to group multiple containers using a Container Group.

## Prerequisites

Make sure the following are set up before you begin:

- An active Azure subscription
- Azure CLI installed (version 2.0.77 or later)
- Docker installed (optional, if you plan to build custom images)
- Logged in to Azure:
  ```bash
  az login
  ```
## Step 1: Create a Resource Group
Create a resource group where all resources will be deployed.

```bash
az group create \
  --name ACI-ResourceGroup \
  --location eastus
```

## Step 2: Deploy a Public Docker Image to Azure Container Instance
We'll use a publicly available Docker image from Microsoft.

```bash
az container create \
  --resource-group ACI-ResourceGroup \
  --name hello-world-container \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --dns-name-label hello-world-container-$RANDOM \
  --ports 80
```

## Step 3: Check Container Status
After deployment, verify that the container is running.

```bash
az container show \
  --resource-group ACI-ResourceGroup \
  --name hello-world-container \
  --query "{FQDN:ipAddress.fqdn, ProvisioningState:provisioningState}" \
  --out table
```

Open the Fully Qualified Domain Name (FQDN) in your browser to confirm the app is working.

## Step 4: Create a Container Group with Multiple Containers

You can run multiple containers together in a single container group. This is done using a YAML configuration.

### 4.1 Create the YAML File
Create a file named `aci-containergroup.yaml` with the following content:

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup
properties:
  containers:
  - name: app1
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGb: 1.5
  - name: app2
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 8080
      resources:
        requests:
          cpu: 1.0
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### 4.2 Deploy the Container Group
Use the following command to deploy the container group:

```bash
az container create \
  --resource-group ACI-ResourceGroup \
  --file aci-containergroup.yaml
```

## Step 5: Test the Deployment
Check the FQDN of the deployed container group:

```bash
az container show \
  --resource-group ACI-ResourceGroup \
  --name mycontainergroup \
  --query ipAddress.fqdn
```
Open the FQDN in your browser using both port 80 and port 8080 to confirm both containers are accessible.


## Step 6: Clean Up Resources
To delete all resources created in this guide, use:

```bash
az group delete --name ACI-ResourceGroup --yes --no-wait
```

## Reference Video

YouTube: [https://www.youtube.com/watch?v=jAWLQFi4USk](https://www.youtube.com/watch?v=jAWLQFi4USk)

## Notes

- All containers within a container group share the same network, IP address, and storage volumes.
- If you want to use private images from Azure Container Registry, you’ll need to authenticate using `az acr login`.
