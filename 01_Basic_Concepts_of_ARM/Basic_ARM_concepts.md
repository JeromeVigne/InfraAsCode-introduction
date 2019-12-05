# Basics of ARM Templates

## What are ARM templates

Every resource in Azure is deployed using the Azure Resource Manager (ARM) API. You can interact with the ARM API in a multitude of ways: 
- The GUI by going to portal.azure.com, 
- PowerShell with the Az module, 
- the Azure CLI, 
- different SDKs (Java, .Net, Python), 
- tools like Terraform
-  **(a well kept secret)** by going to resources.azure.com

The ARM API is a REST API, so we can send complex JSON packages to it. Essentially these packages are the so called *ARM templates*: a structured declarative document containing the list of resources you want the ARM to deploy for you. With that you will be able to deploy all of your azure infrastructure and resources from code and embed it in your favorite CICD tools.

The structure of ARM template is always as such:

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


## Where to start

There are different places to get your hands on to ARM templates:

### When starting fresh:

#### Export template for new resources from the portal
If you create a new resource in the portal, you will always find a link to downlaod the ARM templates on the last blade before creating the resource. 

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Download_a_teamplate_for_Automation.PNG)

You will notice that the template will contain two files. The template.json file contains the resources and a list of parameter elements that match the information you were prompted for in the GUI. The parameters.json file contains the values you supplied to the according parameter elements.

#### Fully documented schemas for each resource type
This page contains the schemas for all resource tyoes organized by namespace and API version: https://docs.microsoft.com/en-us/azure/templates/

#### The Azure quickstart teamplates
Another great source for templates that are ready to use is:
https://github.com/Azure/azure-quickstart-template** Have a look around (use Ctrl + F to text search this enormous repo).


### Capturing existing resources

#### Export template for existin resources:
You can also export the auto-generated ARM templates from existing resources in your Azure portal:

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/ExportTemplate.PNG)

You will notice that when exporting auto-generated temaplates, the file contains a lot of fields, most of which you have likely left as default and therefore would not need specifying in your template. However, this is very helpful when you want to start capturing resources that you have deployed and to which you might have made some setting changes. Those changes will typically show in the exported template.

#### A well kept secret resources.azure.com

Finally you can also interact with the ARM API through resources.azure.com. Find an indivdual resource and you will see all the fields the JSON contains for that resource. This is a great place to go to for more advanced settings that you want to pre-populate when creating your Infrastructure from CICD. But again it contains many fields that are not required.

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/resources_azure_com
.PNG)



 