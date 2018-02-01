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

![alt text](https://github.com/joshcrypt/sendy-deploymentworkflow/blob/master/DeploymentWorkflow.PNG)