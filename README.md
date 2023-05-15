# frontend_deployment_pipeline

### Prerequisite 

- Azure Cloud Account.
- Azure DevOps Account.
- Self hosted Agent to run the pipeline.
- We can create Agent by hosting Virtual Machine on Azure Cloud.    

### Steps to create Agent on Azure DevOps and run the pipeline

Create a Ubuntu latest Virtual machine on Azure Cloud Portal
```
Step 1 - Create the Agent
mkdir myagent && cd myagent

Step 2 - Download the agent
wget https://vstsagentpackage.azureedge.net/agent/2.214.1/vsts-agent-linux-x64-2.214.1.tar.gz  

Step 3 - Configure the Agent
tar zxvf vsts-agent-linux-x64-2.214.1.tar.gz  

Step 4:
Run the below command:

./config.sh

Step 5:
Enter server URL >
https://dev.azure.com/yourorganization

Step 6:
Enter authentication type (press enter for PAT) > PAT

Step 7:
Enter Agent pool
Give some name

Step 8:
Enter Agent name --> myBuildAgent_1

Step 9:
Enter work folder > enter
that's it agent is successfully configured.

Step 10:
Configure the Agent to run as a Service
sudo ./svc.sh install &

Step 11 :Execute now to run as a service
./runsvc.sh &

Step 12 :
Check the status of build Agent
Click on Ubuntu-18-VM pool name
Click on Agents 

```
### Trigger -
A trigger in Azure DevOps refers to an event or condition that automatically starts a pipeline run. In Azure DevOps, pipelines are a way to automate the building, testing, and deployment of applications. When a trigger event or condition occurs, the pipeline is triggered to run, and the defined tasks in the pipeline are executed.
```
trigger:
- master
```

### Pool -
 Agent pool jobs run a job on a single agent. If you need to run a job on all agents, such as a deployment group for classic release pipelines, see Provision deployment groups.
 
```
pool: Ubuntu latest
```

### Jobs -
In the simplest case, a pipeline has a single job. In that case, you do not have to explicitly use the job keyword unless you are using a template. You can directly specify the steps in your YAML file.

```
jobs:
- job: setup_prerequisite

```
### Variables -
In Azure Pipelines, variables refer to values that are defined and used throughout a pipeline. Variables can be used to store values such as connection strings, deployment targets, or any other data that needs to be referenced repeatedly in the pipeline.
```
variables:
  TEST_URL: ${{ variables.TEST_URL }}
  USER_ID: ${{ variables.USER_ID }}
  CLIENT_ID: ${{ variables.CLIENT_ID }}
  CLIENT_SECRET_ID: ${{ variables.CLIENT_SECRET_ID }}

```
### Stages -
You can organize pipeline jobs into stages. Stages are the major divisions in a pipeline: "build this app", "run these tests", and "deploy to pre-production" are good examples of stages. They're logical boundaries in your pipeline where you can pause the pipeline and perform various checks.

```
stages:
- stage: quality
  displayName: Quality
  jobs:
  - job: lint
    displayName: Lint
    pool:
      vmImage: ubuntu-latest
    steps:
    - script: 
        # Add your linting commands here
      displayName: Linting

```
```
- stage: dependency-vulnerability-check
  displayName: Dependency vulnerability check
  jobs:
  - job: dependency-scanning
    displayName: Dependency scanning
    pool:
      vmImage: ubuntu-latest
    steps:
    - script:
        # Add your dependency scanning commands here
      displayName: Dependency scanning
    # Add artifact definition here
```
```
- stage: test
  displayName: Test
  jobs:
  - job: coverage
    displayName: Code coverage
    pool:
      vmImage: ubuntu-latest
    steps:
    - script:
        # Add your code coverage commands here
      displayName: Code coverage
    variables:
      CI: true

```
```
- stage: deploy
  displayName: Deploy
  jobs:
  - job: deployment
    displayName: Deployment
    pool:
      vmImage: ubuntu-latest
    steps:
    - script:
        # Add your deployment commands here
      displayName: Deployment
    variables:
      CI: true
```
```
- stage: multi-browser-test
  displayName: Multi-browser test
  jobs:
  - job: multi-browser-test
    displayName: Multi-browser test
    pool:
      vmImage: ubuntu-latest
    steps:
    - script:
        - pip3 install aitest-cli
        - aitest run -id ${{ variables.TEST_CASE_ID }} -p ${{ variables.GIT_TOKEN }} -w 5
        - aitest status ${{ variables.TEST_CASE_ID }}
      displayName: Multi-browser test
    variables:
      CI: true
```

Requirements

Azure DevOps account

Docker

Pipeline Overview


The pipeline has the following jobs:

### setup_prerequisite
#
This job sets up the Python environment and installs the dependencies needed for the subsequent jobs.

### lint
#
This job uses Docker to run linting tools on the code. It uses yamllint to lint YAML files, tflint to lint Terraform files, and ESLint to lint JavaScript files.

### test
#
This job runs pytest to execute the tests.

### Plan
#
This job initializes and plans the Terraform deployment. It then applies the deployment with the -auto-approve flag.

### Usage
#
To use this pipeline, you will need to:

Set up an Azure DevOps project and repository
Copy the contents of this repository to your project repository
Configure the pipeline YAML file to match your specific infrastructure deployment needs
Add the pipeline to your Azure DevOps project and run it
For detailed instructions on setting up and running a pipeline in Azure DevOps, please refer to the Azure DevOps documentation.
https://learn.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops
