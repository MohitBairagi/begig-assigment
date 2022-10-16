# Begig-Assignment

Synopsis
========
Serverless (SaaS) Architecture with AWS

Design
======
1) Serverless SaaS application is decoupled into two parts Frontend and Backend.
2) Frontend component is hosted on AWS Amplify. AWS Amplify is a set of purpose-built tools and features that enables frontend web and mobile developers to quickly and easily build full-stack
applications on AWS. Amplify suppors different frontend libraries like react.js, angular.js etc.
3) Above frontend component is backened by APIs running on API Gateway and lambda with node.js runtime.
4) Amplify service makes it easy to integrate frontend with backend by using amplify cli

CI/CD Strategy
==============
1) From the Architecture diagram can be easily seen that we are using AWS multiaccounts strategy for serverless SaaS hosting. Master account is the controller account which runs
all pipelines to deploy application in both NonProd and Prod Environment and we are maintaining different environment in different accounts to avoid quota hitting issues and 
possibility of resource deletion assuming them as dev resources.
2) When developer merge the pr in main branch then it triggers the pipeline in master account which performs different actions as following:
- Build a docker image which is used to run tests and deploy application resources.
- Run unit tests and plan deploy(terraform feature) in parallel to validate if all tests get passed and plan deploy result is expected.
- Master account deploy itself known as bootstrap
- deploy nonprod environment in nonprod account and run all integration tests to validate application is running.
- In the end deploy prod environment
3) CodePipeline, CodeBuild is used with ECR and some other aws services to deploy infra resources and run all tests.

![alt text](architecture.svg "Title")

Go Live Plan
============
- A go-live is when a particular product/service launched or becomes available for use. A successful go-live requires optimum preparation, specific and timely action, and a contingency plan
to deal with when unexpected issues crop up.
- In our architecture model we are using agile methodologies and CI/CD strategy so following our strategy we launch our product in iterations with small modules.
- Firstly our product will be launched and tested in NonProd environment hence it reduces possibilities of issues to a large extent. Still if there is any issue in production, it 
would be easy to resolve and debug because of small modules addition and good monitoring and fault tracing using X-ray in comparison to launching whole product at once.

Infra as Code
=============
Terraform is the IaC tool that can be integrated with environment. Terraform is a powerful tool that helps the employees work in IT operations, provision, upgrade, and maintain infrastructure.
Terraform spans across multiple Cloud Service Providers like AWS, Azure, Google Cloud Platform, and many more, Terraform covers most of the AWS resources.

Monitoring Tool
===============
**CloudWatch:** As shown in Diagram we are going to use CloudWatch for monitoring purpose. CloduWatch is AWS native service so most of the required metrics are generated and managed by aws itself.
Example: Invocation, Success & Error Count and Duration metrics for lambda are generated and managed by aws.

**X-ray:** X-ray is another AWS service for Error tracing and easy debugging. It is quite easy to integrate with aws services API Gateway and lambda. One just need to enable the X-ray tracing on 
aws resources. For faster debugging subsegments can be generated and pass to X-ray daemon using X-ray SDK which makes much faster to debug application in case of any error/fault.
Secondly, X-ray Group and X-ray insight really helps in notification automation using cloudwatch event. So, if X-ray notice any unusual behaviour then on call person can get notified and error 
can be resolved.