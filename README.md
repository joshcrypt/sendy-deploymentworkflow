# SENDY Continuous Integration / Continuous Deployment Workflow
## Introduction

This dcocument details the proposed workflow for the continuous integration and continuous deployment of code to production in a containerized micro-service environment at Sendy.

Deploying iterations quickly provides a competitive advantage and allows manageable releases with each Sprint. This enables developers to create fixes quickly and build new features efficiently. 
To achieve successful continuous deployments the whole deployment process needs to be automated and the changes should be well tested and  end-to-end monitoring must be in place.

## Set Up for Continuous Integration / Continuous Deployment

For this setup the Amazon AWS Cloud Environment will be used. The services and tools which will be used include
* Git Repository
* Amazon Code Pipeline
* Amazon Cloud Formation
* Amazon Code Build
* Amazon Code Deploy
* Amazon Elastic Container Service (ECS)
* Amazon Elastic Cloud Compute (EC2)
* Amazon Virtual Private Cloud (VPC)
* Amazon Elastic Container Registry (ECR)
* Amazon Route 53

![alt text](https://github.com/joshcrypt/sendy-deploymentworkflow/blob/master/DeploymentWorkflow.PNG)

The overall design for the deployment workflow is as shown in the [figure](https://github.com/joshcrypt/sendy-deploymentworkflow/blob/master/DeploymentWorkflow.PNG) above.

## Components and Services Description

The description for the DevOps Tools and Services used to achieve Continuous Integration are explained within this section. The workflow follows the [figure](https://github.com/joshcrypt/sendy-deploymentworkflow/blob/master/DeploymentWorkflow.PNG) shown above in the previous section.

### Sendy Coders
The developer/developers working on a feature or fix are responsible for developing, building and deploying code to a centralized repository. This repository is the Git repo and is explained in detail in the next section. Once a Sprint starts, developers and QA collaborate to ensure end to end tests on the feature or fix are without fault before deploying to production.
To minimize bugs in the code qualitative analysis and regression and smoke tests need to be done while in the testing phase before pushing to the staging environemt.
Once the testing phase is proven to be okay, the code should be tested in the staging environment and Non Functional tests done.
Once the fix/feature is deemed deployable it is deployed to the Git repository. This is the trigger for initiating the next step of the automated continuous deployment.

###### The steps involved by coder/developers are shown below
* Developer creates a fix / feature
* Tests are done on the feature / fix until it is deemed deployable
* Developer commits and pushes code to a centralized Git repository
* The code pushed to the Git repo initiates the automated deployemnt in the AWS CodePipeline

### GitHub code - Sendy
The GitHub repo is the centralized repository where developers push their code. Once code has been pushed to the github repository, AWS CodePipeline detects the new deployments and automates the Code Build phase, the deployment to the containerized Elastic Container Registry and Elastic Container Service environments and the changes can be viewed from the container service URL in Cloud Formation.

###### The steps involved in the github section are shown below
* Developers push code to GitHub repository
* Automated deployment is triggered once code has been successfully pushed to the repository
* CodePipeline detects any new code and automated deployment to ECS containerized environment.

### AWS CodePipeline
The AWS CodePipeline polls the github repository to check if any new code has been checked in. When a new version is found a trigger is kicked in to create a Docker image using CodeBuild in AWS Elastic Container Registry (ECR) using the code from github. The CodePipeline initiates the building of the Docker container from the github code then creates it in AWS ECR, which leads to the automation of the continuous deployment lifecycle.

###### The steps involved in the AWS CodePipeline section are shown below
* CodePipeline polls the GitHub repository for any new code.
* Once it finds code, it initiates the creation of a docker image by triggering CodeBuild which is responsible for packaging the code into a containeerized Docker image.

### AWS CodeBuild
AWS CodeBuild is integral to continuous deployment because it removes the need to provision, manage and scale build servers. Once CodePipeline gets the code from the github repository it pushes it to AWS CodeBuild which creates a Docker containerized image of the Code. This image is then packaged and the build information is added to the AWS ECR repository.

###### The steps involved in the AWS CodeBuild section are shown below
* CodePipeline fetches the code from the github repo and passes it to CodeBuild
* CodeBuild packages the code and adds build information and stores it as a docker container image in AWS Elastic Container Registry (ECR)

### AWS CloudFormation
AWS CloudFormation is used to automate the creation of AWS services and infrastructure using predefined templates. The template in this contains a fully configured Amazon Virtual Private Cloud environment, with Amazon Elastic Load Balancers, Amazon Elastic Compute Cloud, Amazon Elastic IPs, Amazon Container Service and Amazon Container Registry.  AWS CloudFormation is an integral part to Sendy's continuous deployments because it can be used to update existing environments with new code and it can also be used to spin up new environments with new code in containers. Once CodeBuild packages the new code in a Docker Containerized Image, CodePipeline starts the update of AWS CloudFormation stack, which has definitions of the Elastic Container Service.
CloudFormation references the build definition and creates a task to update the ECS Service with the new build information.

###### The steps involved in the AWS CloudFormationd section are shown below
* Upon creation of a packaged Docker Container Image in Elastic Container Registry, CodePipeline initiates the update of Elastic Container Service using CloudFormation. 
* CloudFormation uses the build information to create a task definition for updating/creating Elastic Container Service.
* Cloud Formation is a DevOps tool that is used to automate infrastructure and service creation.

### AWS Elastic Container Service
The Docker Container is packaged inside an AWS Elastic Container Service instance. Once CloudFormation creates a task definition to update the stack, it then informs AWS Elastic Container Service to fetch the docker image from AWS Elastic Container registry in order to replace the old task with the new task.
Once the docker image is deployed in the containerized environment it can then be tested using the serivce URL provided by the CLoudFormation step.
The clustered EC2 instances can be cofigured to Route 53/ or DNS registrar to have TTLs set to 60 seconds, such that there is no downtime and the DNS record can be pointed to the correct cluster.
CodePipeline provides a dashboard to be able to view the deployment steps and stages.

###### The steps involved in the AWS Elastic Container Service section are shown below
* Amazon CloudFormation creates a task to replace the existing task with a new task
* Amazon ECS fetches docker image from Amazon ECR and updates it's content
* Test from service URL to check if the deployment is successful
* Monitor on the dashboard to view the progress of the deployment.

### Monitoring and Logging
It is important to ensure monitoring and logging of the deployed changes is done efficiently and across the whole deployment process. This should be done in order to get metrics for comparison and to create a proper baseline for consistency.

## Summary
Once coders have developed and tested a fix or a new feature they deploy code to a centralized gihub repository. Once this is done CodePipeline has a poll for checking the most recent changes on the git repository. CodePipeline informs CodeBuild which packages the code and creates a containerized docker image of the code and stores it in the Elastic Container registry repository. CodePipeline then triggers CloudFormation to update the stack by having the Elastic Container Service fetching the image from Elastic Container registry. The deployment can be monitored on the pipeline and also if it successful the change should be tested via the service URL. Amazon Route53 can be used to set TTLs of 60 seconds pointing to the EC2 instances such that when a deployemnt is done in one cluster the DNS is updated to point to the cluster with the deployed instance, thereby eliminating downtime.
