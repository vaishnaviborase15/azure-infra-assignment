Create an App Service Plan, Provision a Web App, and Deploy a Simple Welcome Page
This guide explains how to create an App Service Plan, provision a Web App, and deploy a static HTML welcome page using Azure CLI.

Step 1: Login to Azure
Run the following command to log in to your Azure account:
az login

Step 2: Create a Resource Group
Create a new resource group where all the resources will be placed:
az group create --name MyWebAppResourceGroup --location eastus

Step 3: Create an App Service Plan
Create an App Service Plan with the Free tier:
az appservice plan create --name MyAppServicePlan --resource-group MyWebAppResourceGroup --sku FREE

Step 4: Create a Web App
Provision a web app within the created App Service Plan. Make sure the web app name is globally unique:
az webapp create --name MyUniqueWebAppName1234 --resource-group MyWebAppResourceGroup --plan MyAppServicePlan

Step 5: Create a Simple HTML Welcome Page
Create a file named index.html with the following content:

<!DOCTYPE html> <html> <head><title>Welcome</title></head> <body> <h1>Welcome to My Azure Web App</h1> <p>This is a simple HTML page deployed to Azure App Service.</p> </body> </html>
Step 6: Zip the HTML File
Create a folder named site, move index.html into it, and zip the content:
cd site
zip site.zip index.html

Step 7: Deploy the HTML Page to the Web App
Deploy the zipped HTML content to the Azure Web App using:
az webapp deployment source config-zip --resource-group MyWebAppResourceGroup --name MyUniqueWebAppName1234 --src site.zip

Step 8: Access the Web App
Open a browser and go to the URL:
https://MyUniqueWebAppName1234.azurewebsites.net

Step 9: (Optional) Clean Up Resources
To delete all resources once you're done, run:
az group delete --name MyWebAppResourceGroup --yes --no-wait

This completes the task of provisioning a web app and deploying a welcome page to it on Azure.
