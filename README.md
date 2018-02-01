# SENDY Continuous Integration / Continuous Deployment WORKFLOW
## Introduction

This dcocument details the procedure for the continuous integration and continuous deployment of code to production in a containerized micro-service environment.

Deploying iterations quickly provides a competitive advantage and allows manageable releases with each Sprint. This enables developers to create fixes quickly and build new features efficiently. 
To achieve successful continuous deployments the whole deployment process needs to be automated and the changes should be well tested and  end-to-end monitoring must be in place.

## Set Up for Continuous Integration / Continuous Deployment

For this setup the Amazon AWS Cloud Environment will be used. This whole design is serverless. The services which will be used include
* Git Repository
* AWS Code Pipeline
* AWS Cloud Formation
* AWS Code Build
* AWS Code Deploy
* AWS ECS
* AWS EC2
* AWS VPC

The overall design for the deployment workflow is as shown in the [figure](https://github.com/joshcrypt/sendy-deploymentworkflow/blob/master/DeploymentWorkflow.PNG) above.

## Components and Services Description

The description for the DevOps Tools and Services used to achieve Continuous Integration are explained within this section. The workflow follows the figure shown above in the previous section.

### Sendy Coders
The developers working on a feature of fix are responsible for developing, building and deploying code to a centralized repository. This repository is the Git repo and is explained in detail in the next section. Once a Sprint starts, developers and QA collaborate to ensure end to end tests on the feature or fix are without fault before deploying to production. 
Once the fix/feature is deemed deployable it is deployed to the Git repository. This is the trigger for initiating the next step of the automated continuous deployment.

###### The steps involved by coder/developers are shown below
* Developer creates a fix / feature
* Tests are done on the feature / fix until it is deemed successful
* Developer commits and pushes code to a centralized Git repository
* The build pushed to the Git repo initiates the automated deployemnt in the AWS CodePipeline

### GitHub code - Sendy
This is the centralized repository where developers push their code
