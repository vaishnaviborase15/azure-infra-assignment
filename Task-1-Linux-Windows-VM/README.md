# Deploy Linux and Windows Virtual Machines in Azure and Access Them via SSH and RDP

This guide covers the steps to deploy a Linux virtual machine and a Windows virtual machine in Azure using the Azure CLI, and how to access them using SSH and RDP respectively.

## Prerequisites

- Azure CLI installed or use Azure Cloud Shell
- Azure subscription
- SSH client (built-in on Linux/macOS and available via PowerShell or Git Bash on Windows)
- RDP client (Remote Desktop Connection on Windows or Microsoft Remote Desktop on macOS)

## Step 1: Login to Azure

az login

Select your Azure subscription when prompted.

## Step 2: Create Resource Groups

Create separate resource groups for Linux and Windows VMs.

az group create --name RG-Vaishnavi-Linux --location eastus  
az group create --name RG-Vaishnavi-Windows --location eastus

## Step 3: Deploy a Linux Virtual Machine

az vm create \
  --resource-group RG-Vaishnavi-Linux \
  --name VaishnaviLinuxVM \
  --image Ubuntu2204 \
  --admin-username vaishnavi \
  --generate-ssh-keys

Note: The command above generates SSH keys in the default path (~/.ssh/).

## Step 4: Get Public IP of Linux VM

az vm list-ip-addresses --resource-group RG-Vaishnavi-Linux --name VaishnaviLinuxVM --output table

Copy the public IP address.

## Step 5: SSH into Linux VM

ssh vaishnavi@<public-ip>

Replace <public-ip> with the actual IP from the previous step.

## Step 6: Deploy a Windows Virtual Machine

az vm create \
  --resource-group RG-Vaishnavi-Windows \
  --name VaishWinVM \
  --image Win2022Datacenter \
  --admin-username vaishnavi \
  --admin-password vaish@1234

Note: Replace the password with a strong password that meets Azure complexity requirements.

## Step 7: Get Public IP of Windows VM

az vm list-ip-addresses --resource-group RG-Vaishnavi-Windows --name VaishWinVM --output table

Copy the public IP address.

## Step 8: RDP into Windows VM

Open Remote Desktop Connection (mstsc) on your local machine.  
Enter the public IP address of the Windows VM.  
Use the credentials set during VM creation:
Username: vaishnavi  
Password: vaish@1234

## Step 9: Optional - Open Ports if Needed

If you face connection issues, make sure the necessary ports are open.

For Linux (SSH uses port 22):

az vm open-port --port 22 --resource-group RG-Vaishnavi-Linux --name VaishnaviLinuxVM

For Windows (RDP uses port 3389):

az vm open-port --port 3389 --resource-group RG-Vaishnavi-Windows --name VaishWinVM

## Cleanup (Optional)

az group delete --name RG-Vaishnavi-Linux --yes --no-wait  
az group delete --name RG-Vaishnavi-Windows --yes --no-wait
