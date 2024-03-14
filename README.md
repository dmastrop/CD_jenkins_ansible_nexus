# Prerequisites
- JDK 11 
- Maven 3 
- MySQL 8

# Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- Tomcat
- MySQL
- Memcached
- Rabbitmq
- ElasticSearch
# Database
Here,we used Mysql DB 
sql dump file:
- /src/main/resources/db_backup.sql
- db_backup.sql file is a mysql dump file.we have to import this dump to mysql db server
- > mysql -u <user_name> -p accounts < db_backup.sql

# reference information
use cloudnetworktesting.com for all servers with A records

# Project staging
note project8 is manual servers Nexus, Sonarqube and Jenkins with basic pipeline. Servers are on EC2.
Uses slack. 
The branch is ci-jenkins

project9 is using  AWS Paas CodeBuild, CodeCommit, CodeArtifact and AWS Pipeline to do the same thing, build the CI artifact and deploy to S3 instead of Nexus repo. Note SonarCloud instead of Sonarqube
Uses AWS SNS to email

project 10 continues with project 8 EC2, but extends CI to CD and utlizes ECS/ECR for the deployment of docker container images to ECR and then running them on ECS as deployment of the artifact.  The first part creates the .war artifact in stage1 of docker image build and then places the .war in the default directory of a stage2 tomcat docker image.   The container is then placed on ECS as a running instance using Cluster, Task and then placing task into the ECS Service
CD is done in two parts a Staging image for QA and then a production image for actual release
Uses Slack
The branch is cicd-jenkins which uses the Staging/Jenkinsfile

Branch production-jenkins used for the production deployment of the docker image (tomcat with vprofile app) to ECS.
The ProdPipeline/Jenkinsfile will be used for this pipeline. We will create a new pipeline for production deployment separate from the one that uses cicd-jenkins staging.


