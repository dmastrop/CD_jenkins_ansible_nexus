## ci-cd branch README 

# high level overview
CI project(ci-jenkins branch): fork of hkhcoder/vprofile-project. Jenkins, nexus, and sonarqube CI pipeline setup. All backend servers created on AWS EC2 instances(bash script provisioning)&build config params set in pom.xml & settings.xml. 
CD(cicd-jenkins branch): CD added w/ AWS ECR & ECS, docker for staging. (using staging ECS cluster). This part of staging includes dockertizing the .war file into a tomcat docker image (using 2 stage Dockerfile built from current source code)
CD2(production-jenkins branch): CD added for deployment of the docker image to container on ECS production cluster

SEE README in cicd-jenkins (this file) for detailed pipeline flow (see below)


# Project staging
note project8 is manual servers Nexus, Sonarqube and Jenkins with basic pipeline. Servers are on EC2.
Uses slack. 
The branch is ci-jenkins

project9 is using  AWS Paas CodeBuild, CodeCommit, CodeArtifact and AWS Pipeline to do the same thing, build the CI artifact and deploy to S3 instead of Nexus repo. Note SonarCloud instead of Sonarqube
Uses AWS SNS to email

project 10 continues with project8 EC2, but extends CI to CD and utlizes ECS/ECR for the deployment of docker container images to ECR and then running them on ECS as deployment of the artifact.  The first part creates the .war artifact in stage1 of docker image build and then places the .war in the default directory of a stage2 tomcat docker image.   The container is then placed on ECS as a running instance using Cluster, Task and then placing task into the ECS Service
CD is done in two parts a Staging image for QA and then a production image for actual release
Uses Slack
The branch is cicd-jenkins for staging CD
The branch is production-jenkins for production CD


# Detailed pipeline flow per branch
-First developers make changes to the cicd-jenkins branch

-push to github: this will trigger the webhook ci pipeline (first one)

-(ci-jenkins branch) Jenkins pipeline start(CI)
This will run checkstyle and sonarqube analysis 
Quality Gates will be attached to the project in sonarqube and based upon the QG criteria the build will either pass or fail. If it fails the develpers will be notified on the relevant Slack channel (as configured in the post stage in Jenkinsfile)
(and if it passes, developers and QA will be notified on the slack channel; staging CD will kick in and  will dockertize the image and then put it on ECR and also deploy to the staging ECS cluster (not the production ECS cluster) for QA testing (Scale, stress, functional, performance, soak testing, etc))

-Nexus repo will also have the build. See below ...as part of CI pipeline
Note that Nexus only has the .war file (artifact) not the docker image. (docker image will be created and placed on ECR at a later stage)

-(cicd-jenkins branch) Start CD staging of pipeline: ECR: Size changed so there is a new line on ECR registry but it is the same code basically
Note that this is the docker image, not just the .war. The .war has been incorporated into a tomcat docker image using 2 stage Dockerfile as part of CD integration stage.
Note build number as shown in the Jenkins pipeline Web UI  and in Nexus repo 
The build tags need to be consistent throughout the pipeline stages so that if there are issues the build can be traced consistently all the way back in the pipeline. This makes the logging much more concise for tracebacks in case of failure.

-(cicd-jenkins branch) CD staging: this docker image is now deployed to the ECS Staging(not production) cluster
This is so that QA can start testing the build from ECR. QA testing consists of: QA testing (Scale, stress, functional, performance, soak testing,integration testing, system testing,  etc). The running container in the ECS staging clustser will be replaced with this latest version and should have an updated timestamp.  The instance is fully accessible via an ALB (Applciation Loadbalancer) on AWS and can be functionally tested. QA will have additional testbeds on which to do more extensive testing with the build on ECR.  ECR has a private URL for accessibility throughout the testing organzation.
If it passes through this stage the post Jenkins stage will send slack message on the project channel on the passing status

-(production-jenkins branch) If QA passes the build then it will go on to CD production which is the ECS production cluster
The build will be deployed to the Production ECS cluster (there is no rebuilding of the build from source code if it gets to this stage) only if QA approves the build.
The running ECS cluster docker image should be replaced via a new timestamp. The old container that is running will be replaced
Slack notification will be sent out once the docker image is deployed to the production ECS cluster.

The staging code will need to be merged with the production code (production-jenkins main branch)
Git checkout production-jenkins
Git merge cicd-jenkins  (To merge the cicd-jenkins branch with the production-jenkins main branch)
This will create a pull request on github repo.
Do smoke testing and sanity testing
Once approve the pull request the pipeline will be triggered

Overall the Git push origin production-jenkins will trigger the production pipeline in Jenkins (pipeline #3) and this will deploy the docker image to the production ECS cluster


















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


