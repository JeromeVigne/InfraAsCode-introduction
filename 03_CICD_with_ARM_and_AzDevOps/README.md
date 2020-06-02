# Introducing Continuous Deployment with ARM

Now that you have invested time in making sure all your deployments are defined with immutable and declarative artifacts, you need to think about a strategy to automate the deployment of the aforementioned artifacts.

There are many tools for Continuous Integration (CI) and Continuous Deployment (CD), if you have started going the ARM route because you are targeting Azure, it is a natural fit to use Azure DevOps for CI and CD.

In this tutorial we will focus on the CD part with Azure pipelines in Azure DevOps.

## Let's set up Azure DevOps

If you already have an organization in Azure DevOps, just create a new project, otherwise navigate to dev.azure.com and create an organization. Then create your first DevOps project. Once your project is created you will have access to the boards, the repository, pipelines and many more features of DevOps.

For this exercise we will focus on using the repo and the pipelines. 
> Keep in mind that Az DevOps is very open and although it can provide you with all the capabilities for a thorough DevOps end-to-end platform (PM tooling for sprint planning, bugs and work items tracking, project wiki, git repos, pipelines, test artifacts and thorough reporting...), you can use one or more of these features and mix it with the tools you already use or have grown to like (i.e. Jira, Octopus, Jenkins...).

Navigate to the repository section:
![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Empty_Repo.PNG)

### Working with Git

Here are the commands to start with git, you can also use a UI git tool if you prefer or the integrated source control experience in VSCode.

Copy the URL to clone the repository to your computer.

Navigate to an empty folder on your machine and open the empty folder in VSCode, open a terminal session and run
`git init`, `git clone <YOUR REPO URL>`. You should receive a warning that you are cloning an empty repo.

Now create a file in that empty local folder and save it. In the command cd into the file that the git clone has created. Run `git add .`, this will stage all your files in the folder for the commit. You can specify a specific file instead with `git add <YOUR FILE NAME>`. Finally run `git commit -m <YOUR COMMIT MESSAGE>` and `git push`.

The whole thing should look like this:

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/VSCode-gitinit-screen.PNG)

The above is the manual way (which I will argue is really important to understand). VSCode has also a GUI for this. Once you have run `git init` and `git clone <YOUR REPO URL>`, you can switch and start using the GUI:

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/git-gui.PNG)

As you see, you can hover over changes, stage them, add a message, commit and push all.

For this exercise let's push the working arm template for a storage account from chapter 2 and the parameter file from chapter 2 to your repo. Simply add the files to your local folder, stage them, commit them and push them!

### On to the pipelines

Alright, we now have in our repo a working deployment file and a working parameter file. Let's build the pipelines that will continuously deploy these to Azure.

Here we have multiple ways to do it. For the sake of this exercise we will use a build pipeline and a release pipeline. In some cases for infra as code you could get away with just using the release pipeline.

#### 1. The build pipeline

Navigate to Pipelines (little rocket icon) and create your first pipeline. You should be here:
![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/pipelines-initiate.PNG)

> Here you can see how open AzDevOps is, we will let you trigger the pipeline form all repositories that support git.

Let's select Azure Repos, select the repo and create a starter pipeline. This will create a yaml file that describes what your build pipeline will do.

The yaml file contains the trigger (i.e. when a change occurs on Master branch), and the pool on which it will run (i.e. Ubuntu host that will be spun up for you when needed and removed afterwards).

Remove everything under steps and place your curser underneath. Now click **Show Assistant**. This assistant is awesome and will generate the YAML you need from a GUI guiding you.

Search for "publish build artifacts", you can use the default settings for now and click add. 

>Note that the "Path to publish" is a variable! In fact $(Build.ArtifactStagingDirectory)is a very powerful variable: The local path on the agent where any artifacts are copied to before being pushed to their destination. To learn more about predefined pipeline variables and how to use them follow this [LINK](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml).

So if this will publish the artifacts from $(Build.ArtifactStagingDirectory) we need to make sure we copied the files we wanted into that location in the previous pipeline step. Place your curser above the PublishBuildArtifacts taks and search for copy files. If you leave the Source folder empty it will be at the root of your repo (which is what we want for now). For the target folder paste $(Build.ArtifactStagingDirectory).

The pipeline should look like this:

```yaml
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: CopyFiles@2
  inputs:
    Contents: '**'
    TargetFolder: '$(Build.ArtifactStagingDirectory)'
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
    publishLocation: 'Container'

```

Hit Save and run!

**AWESOME YOU JUST RAN A BUILD PIPELINE!** Now every time you push a change to your master branch it will trigger the build pipeline and publish the latest artifact.

#### 2. The release pipeline

Navigate to pipelines > releases. New pipeline, empty job. In the artifacts section, click the "Add an artifact". Select your source, there should only be one.

If you want continuous deployment (deploy to Azure every time a new build is available) > click on the little thunder symbol at the top right corner of your artifact and select Enabled:
![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Release_Pipeline_Start.PNG)

Now click on Stage 1 where it says 0 tasks. In Stage 1 you have an empty agent job. The agent is the box that will execute the pipeline for us. Add an agent job and look for "ARM template deployment". Add it to your pipeline.

![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Release_Pipeline_ARM.PNG)

Under Resource Manager connection, select manage connection > create a Resource Manager Connection. You do need to be able to register applications in your AAD to do this step. If you are not able to do this, you might have to work with someone who can (typically an admin). Select your subscription and give it a name. You can restrict the connection to a specific resource group if you want. But beware that if you do so, this connection can only see and alter the specific resource group.

Once you are done, return to your pipeline. Select the connection, subscription, a resource group and a location. For Template, select the ellipsis and pick your template json file from th epublished artifact, for the parameters, pick you parameters file. 

Save and create the release.

**WELL DONE, YOU NOW HAVE CREATED A TEMPLATE, PUSHED IT TO A SHARED REPO, TRIGGERED A BUILD AND A RELEASE**

The above is all you need for end-to-end DevOps. With this skeleton you can build more and more!





