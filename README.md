# Deployment to the app01-prod-project18 app server for production deployment

This is the cd-ansible-prod-project18 branch. The jenkinfile is stripped down and primarily just does a deployment
to the production app server.

This assumes that QA has passed the build. The source code is built from the cd-ansible-project18 staging server branch
and not this cd-ansible-prod-project18 production branch.
The jenkinsfile in this production branch uses ansible to deploy the artifact from the staging pipeline to
the production app server (another EC2 instance)

The key difference between this third pipeline on the jenkins server and the second pipeline is that this 
third pipeline has "Build with parameters" in the jenksins file. This is a stage for the user to input the 
artifact from the staging pipeline that has been approved by QA into the deployment pipeline so that it can be
deployed to the production app server. 

The env vars are BUILD and TIME and are mapped into the .war file labeling
For example, the current nexus artifact build from the staging CD pipeline is 10-24-04-24_0147, i.e. BUILD 10
and TIME 24-04-24_0147

The production pipeline is parameterized so these values need to be inputted and then the pipeline run to put
the artifact into production.

The deployment uses ansible again.   The staging.inventory in the staging CD uses route53 internal hostname 
app01staging.vprofile.project18 to instruct ansible where to deploy the staging artifact.
The prod.inventory has the route53 app01prod.vprofile.project18 hostname to instruct ansible where to deploy
the production artifact.

NOTE once again that only the frontend of the application stack is deployed in this project. The rest of the
stock will be deployed by ansible in a later project.

NOTE that CD staging branch push will activate the second pipeline in jenkins to deploy artifact to staging
CD production branch push will nto activate the third pipeline. This is done by using the parameter inputs above, 
BUILD and TIME to instigate the pipeline and put the artifact onto the production server.....


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


