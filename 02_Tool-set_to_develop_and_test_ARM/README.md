# Setting up your work bench

There are many tools with which you can develop ARM templates. From my personal experience this is what I use to develop, test and deploy the templates really quickly. Here is an illustration of the process:

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Workbench.png)


## My choice of tools
I like to work with the following tool set (all of which are free and work on Win, macOS and Linux):
- [VS Code](https://code.visualstudio.com/)
- [The Azure Resource Manager (ARM) Tools extension for VSCode](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
- [The Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- An [Azure subscription](https://azure.microsoft.com/en-us/free/) to which you are at least a *contributor* for testing the deployments
- An [Azure DevOps](https://dev.azure.com) project that I will use for my repository and for the deployment pipelines

## Let's deploy

### Log in and deploy a Resource Group
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

**SWEET!** You deployed your first Resource Group

### Deploy the first resource

Let's deploy a dynamic App Service Plan - you will encur no costs. Use the code in app-service-deploy.json. You can copy paste it from GitHUb into a new file you create in the local folder you are in. Make sure you click on raw option when viewing the file in GitHub.

After you save the file locally, validate that it will run:

`az group deployment validate -g <RGNAME> --template-file <TemplateFilePath>`

You will be prompted for the App_Service_Name, since it is a parameter the template needs.

If it succeeds then let's deploy it:

`az group deployment create -g <RGNAME> --template-file <TemplateFilePath>`

Navigate to your resource group in the azure portal and watch the deployment execute (click on DEPLOYMENTS under Settings):

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/watch_deployment.PNG)

> Once your deployments will be more complex and contain multiple resources, this will be a good place to gather informations like detailed error messages and find aout which resource takes how much time to deploy.

### Template parameters

When a template uses parameters you can either have it prompt you at deployment time or have a parameter file in your source control and point to it at deployment. A parameter file is structured as such:
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
> Parameter files are super usefull when working with across multiple environments. Ideally you maintain **one deploy.json template with multiple param.json** files that contain the parameters for your dev, stage and prod environment (i.e. dev.param.json, uat.param.json and prod.param.json). **Keeping the same deploy.json across all environemnts ensures that no configuration drifts are introduced across environments.**

Now let's move to something a bit more complex that uses variables and parameters.

Create a local file called param.json file that contains the above sample code.

Now we will deploy a storage account from a template in the azure quickstart template repository:
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json

Deploy using a remote template with a local the parameter file:

`az group deployment create -g <RGNAME> --template-uri <TemplateURL> --parameters @<ParameterFilePath>`

> You can mix and match having local temaplte and parameters, having both remotely or a mix of them. You can also reference a parameter file and over-write some parameter values by specifying them in the command: `az group deployment create -g <RGNAME> --template-uri <TemplateURL> --parameters @<ParameterFilePath> --parameters MyParameter=paramvalue`

