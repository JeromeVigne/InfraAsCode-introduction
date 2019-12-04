# Infrastructure as code - Getting started

### My choice of tools
I like to work with the following tool set (all of which are free and work on Win, macOS and Linux):
- Install VS Code (https://code.visualstudio.com/)
- Install the Azure Resource Manager (ARM) Tools extension for VSCode (https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
- Install the Azure CLI (https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- An Azure subscription to which you are at least a contributor

### Deploy your resource group
In Vs Code open a terminal Window and log in to azure: 

`az login`

Get a list of subscriptions you have access to: 

`az account list`

> **Make the response user friendly:** The default response format is json, you can "pipe" it to a table with `az account list --out table` or **change your default settings** (I recommend that) using: `az configure`. Only change the output format to table, the rest stays the same.

Set the subscription we will work with today with:

`az account set -s <SUBSCRIPTION NAME OR SUBSCRIPTION ID>`

Let's create a resource group we will deploy to:

`az group create -n <RGNAME> -l <REGION>`
> A Resource Group (RG) is a logical container of resources. A resource always resides in a RG. A RG always resides in an Azure region, **although the resources it contains do not have to be in that same region**. To get a list of all regions, run: `az account list-locations`

**BRILLIANT!** You deployed your first Resource Group

### Basic understnading of ARM Templates

Every resource in Azure is deployed using the ARM API. You can interact with the API in multiple ways: The GUI by going to portal.azure.com, PowerShell, the CLI (like we just did) and **(a well kept secret)** by going to resources.azure.com (we will make use of this in more advanced chapters, but go and have a look)!

Here we will use the CLI to send json docs to the API. Those are refered to as *ARM templates*.

**The best source for templates to get started is:
https://github.com/Azure/azure-quickstart-template** Have a look around (use Ctrl + F to text search this enormous repo).

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