# Basics of ARM Templates

## What are ARM templates

Every resource in Azure is deployed using the Azure Resource Manager (ARM) API. You can interact with the ARM API in multiple ways: 
- The GUI by going to portal.azure.com
- PowerShell with the Az module 
- the Azure CLI
- different SDKs (Java, .Net, Python)
- tools like Terraform
-  **(a well kept secret)** by going to resources.azure.com

The ARM API is a REST API, so we can send complex JSON packages to it. Essentially these packages are the so-called *ARM templates*: a structured declarative document containing the list of resources you want the ARM to deploy for you. With that you will be able to deploy all of your azure infrastructure and resources from code and embed them in your favorite CICD tools.

The structure of ARM template is always the following:

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
For details on each element navigate to the official documentation: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates


## Places to get started with your templates

There are different places to access the ARM templates:

### When starting fresh:

#### 1. Export template for new resources from the portal
If you create a new resource in the portal, you will always find a link to downlaod the ARM templates on the last blade before creating the resource. 

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Download_a_teamplate_for_Automation.PNG){:height="1091px" width="1240px"}

You will notice that the template will contain two files. The template.json file contains the resources and a list of parameter elements that match the information you were prompted for in the GUI. The parameters.json file contains the values you supplied to the respective parameter elements.

#### 2. Fully documented schemas for each resource type
This page contains the schemas for all resource types organized by namespace and API version: https://docs.microsoft.com/en-us/azure/templates/

#### 3. The Azure quickstart teamplates
Another great source for templates that are ready to use is:
https://github.com/Azure/azure-quickstart-template** Have a look around (use Ctrl + F to text search this enormous repo).


### Capturing existing resources

#### 4. Export template for existing resources:
You can also export the auto-generated ARM templates from existing resources in your Azure portal:

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/ExportTemplate.PNG)

>You will notice that when exporting auto-generated templates, the file contains a lot of fields, most of which you have likely left as default, and therefore would not need specifying in your template. However, this is very helpful when you want to start capturing resources that you have deployed and to which you have made some setting changes (i.e. firewall settings).

#### 5. A well kept secret resources.azure.com

Finally you can also interact with the ARM API through resources.azure.com. Find an indivdual resource and you will see **all the fields the schema contains for that resource type**. 

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/resources_azure_com.PNG)

>resource.azure.com is a great place to visit for more advanced settings that you want to pre-populate when creating your templates. Again it shows all the fields available for the schema of the resource type - most of these are not required for your templates to deploy succesfully.

 