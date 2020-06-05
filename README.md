# Sample CDN WebApp

Static site image content is royalty free and available for any use through Pexels, see credits.txt but has been modified to include:

1) Desktop site with full size images.
2) Mobile site with compressed size images.
3) CSS objects on a storage account leveraging static website feature.
4) Script to automate CDN sample website deployment (see below).

# Deployment
**1. Use Azure Cloud Shell**

Use https://shell.azure.com/ to execute the steps below. Make sure shell is using *Bash* because sequence below does not work on CLI running on PowerShell.
<pre lang="...">
#!/bin/bash
#1. Defined variables. By default generates random Webapp name on Resource Group CDN-LAB. You may change mywebapp$RANDOM or RG to any name you wish.
Webappname=mywebapp$RANDOM
RG=CDN-LAB
</pre>

**2. Build storage and static website to store CSS objects**
<pre lang="...">
az group create --location eastus --name $RG
az storage account create --location eastus --name $Webappname --resource-group $RG --kind "StorageV2" --sku "Standard_LRS"
az storage blob service-properties update --account-name $Webappname --static-website
</pre>

**3. Clone Git Hub repository to local cloud shell and change index.html to specify CSS objects on Static Website on Storage Account**
<pre lang="...">
mkdir quickstart
cd $HOME/quickstart
git clone https://github.com/dmauser/webapp-cdn-sample.git
cd webapp-cdn-sample
strurl=https://"$Webappname".z13.web.core.windows.net
sed -i "s#storage-account-url#$strurl#g" index.html
</pre>

**4. Upload CSS objects to Static Website on Storage Account**
<pre lang="...">
az storage blob upload-batch -s static -d \$web --account-name $Webappname
</pre>

**5. Deploy AppService Plan and Webapp**
<pre lang="...">
az appservice plan create --name $Webappname-plan --resource-group $RG --sku B1
az webapp create --name $Webappname --resource-group $RG -p $Webappname-plan 
az webapp up --location eastus --name $Webappname --resource-group $RG --html
</pre>

**6. Use results of the following command into a browser to see the web app**
<pre lang="...">
echo https://$Webappname.azurewebsites.net
echo Go to Azure Portal and under $RG you will see all created resources.
</pre>

# LAB Clean up
**1. Delete CDN-Storage used to store CSS objects on static website**
<pre lang="...">
az group delete --name $RG
</pre>

**2. Remove Git deployment from Cloud Shell drive**
<pre lang="...">
cd $HOME
rm -r quickstart --force
</pre>