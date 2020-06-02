# Introducing Continous Deployment with ARM

Now that you have invested time in making sure all your deployments are defined with immutable and declarative artifacts, you need to think about a strategy to automate the deployment of the forementioned artifacts.

There are many tools for Continuous Integraton (CI) and Continuous Deployment (CD), if you have started going the ARM route because you are targeting Azure, it is a natural fir to use Azure DevOps for CI and CD.

In this tutorial we will focus on the CD part with Azure pipelines in Azure DevOps.

## Let's set up Azure DevOps

If you already have an organization in Azure DevOps, just create a new project, otherwise navigate to dev.azure.com and create an organization. Then create your first DevOps project. Once your project is created you will have access to the boards, the repository, pipelines and many more features of DevOps.

For this excersice we will focus on using the repo and the pipelines. 
> Keep in mind that Az DevOps is very open and although it can provide you with all the capabilities for a thorough DevOps end-to-end platform (PM tooling for sprint planning, bugs and work items tracing, project wiki, git repos, pipelines, test artifacts and thorough reporting...), you can use one or more of these features and mix it with the tools you already use or have grown to like (i.e. Jira, Octopus, Jenkins...).

Navigate to the repository section:
![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Download_a_teamplate_for_Automation.PNG){:height="1091px" width="1240px"}

### Working with Git

Here are the commands to start with git, you can also use a UI git tool if you prefer ot the integrated source control experience in VSCode.

Copy the URL to clone the repository to your computer.

Navigate to an empty folder on your machine and open the empty folder in VSCode, open a terminal session and run
`git init`, `git clone <YOUR REPO URL>`. You should receive a warning that you are cloning an empty repo.

Now create a file in that empty local folder and save it. IN the command cd into the file that the git clone has created. Run `git add .`, this will stage all your files in the folder for the commit. You can specify a specific file instead with `git add <YOUR FILE NAME>`. Finally run `git commit -m <YOUR COMMIT MESSAGE>` and `git push`.

The whole thing should look like this:
![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Download_a_teamplate_for_Automation.PNG){:height="1091px" width="1240px"}

The above is the manual way (which I will argue is really important to understand). VSCode has also a gui for this. ONce you have run `git init` and `git clone <YOUR REPO URL>`, you can switch and start using the guid:
![image](https://github.com/JeromeVigne/InfraAsCode-introduction/blob/master/images/Download_a_teamplate_for_Automation.PNG){:height="1091px" width="1240px"}

As you see, you can hover over changes, stage them, add a message, commit and push all.

### On to the pipelines

