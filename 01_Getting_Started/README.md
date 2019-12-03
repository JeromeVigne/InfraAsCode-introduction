# InfraAsCode-introduction

## Let's get our workbench set up!
First, let's open Visual Studio Code and open a Terminal session.

Log in to azure: 

`az login`

Get a list of subscriptions you have access to: 

`az account list`

The default response format is json, you can "pipe" it to a table with `az account list --out table` or change your default settings (I recommend that): `az configure`

Set the subscription we will work with today with 

`az account set -s <SUBSCRIPTION NAME OR SUBSCRIPTION ID>`

You are all set to create your first resource!

Let's create a resource group we will deploy to (RG is a logical container of resources. a resource always resides in an RG. An RG always resides in an Azure location, although the resources it contains do not have to be in that same region).

Let's get a list of regions first:

`az account list-locations`

Pick a region you want to work in today.

`az group create -n RGNAME -l REGIONNAME`

**BRILLIANT you have your first Resource Group**

## Deploying you first ARM template

Every resource in Azure is deployed using the ARM API. You can interact with the API in multiple ways! The GUI by going to portal.azure.com, PowerShell, the CLI and other SDKs...

Today wer will use the CLI to send json docs to the API. Does are refered to as **ARM templates**!

A great source for templates to get started is:
https://github.com/Azure/azure-quickstart-templates

Have a look around (use Ctrl + F to text search this enormous repo).

You can also see the auto-generated ARM templates from existing resources in the Azure portal:

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/01_Getting_Started/ExportTemplate.PNG)

 The structure of ARm template is as such:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "functions": [],
    "outputs": {}
}
```
Let's deploy the first storage account. Use the code in most-basic-storage-account.json

Validate that it will run:

`az group deployment validate -g <RGNAME> --template-file PATHToYourLocalARMTemplate`

If it succeeds then let's deploy it:

`az group deployment create -g <RGNAME> --template-file <PATHToYourLocalARMTemplate>`

Navigate to your resource group in the azure portal and wathc the deployment (click on DEPLOYMENTS under Settings)

Now let's move to something a bit more complex, that uses variables and parameter:
https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json

When a Template uses parameters you can either be prompted to provide params at deployment time or better, you have a param file in your source control and provide it at deployment:

A param file is structured as such:
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountType": {
            "value":"Standard_LRS"
            }
    }
}
```
Deploy using a reference to the param file:

`az group deployment create -g <RGNAME> --template-file <pathToYourLocalARMTemplate> --parameters @<pathToYourLocalPARAMfile>`