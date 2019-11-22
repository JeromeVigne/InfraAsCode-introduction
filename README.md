# InfraAsCode-introduction

## Let's get our workbench set up!
First, let's open Visual Studio Code and open a Terminal session.

Log in to azure: `az login`

Get a list of subscriptions you have access to: `az account list`

The default response format is json, you can "pipe" it to a table with `az account list --out table` or change your default settings (I recommend that): `az configure`

Set the subscription we will work with today with `az account set -s <SUBSCRIPTION NAME OR SUBSCRIPTION ID>`

You are all set to create your first resource!

Let's create a resource group we will deploy to (RG is a logical container of resources. a resource always resides in an RG. An RG always resides in an Azure location, although the resources it contains do not have to be in that same region).

Let's get a list of regions first:
`az account list-locations`

Pick a region you want to work in today.

`az group create -n RGNAME -l REGIONNAME`

**BRILLIANT you have your first Resource Group**

## Deploying you first ARM template