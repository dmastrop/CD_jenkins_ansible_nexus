
# Deploying artifact to staging app server app01-staging-project18

The addtion to the CI pipeline consists of CD to staging servers. This branch is for ansible deployment to the app01 staging server.The branch is cd-ansible-project18.

For ansible, first the templates. There are 4 templates for various versions of RHEL/CentOS and ubuntu depending on version.
The templates are *.js files, scripts for setting up tomcat on the server. For this project I am using 
ubuntu20.  Once tomcat is started on staging application server the next step is to deploy the artifact
from the CI pipeline (see ci-jenkins branch). Ansible requires several variables to construct the proper URL
to get the latest version on the vprofile-release nexus repository.
Ansible deploys the artifact to the tomcat server.

This project19 is only implementing the front end of the vprofile stack onto the staging app server.
The rest of the stack will be deployed onto the infra in a later project (using ansbile as well)

The staging app server will be used by the QA team for system, integration, scale, stress, performance, soak and functional testing of the application

Once QA approves the next CD stage to production app server can be done (this is the cd-ansible-prod-project18 branch)

NOTE: the version of the artifact deployed to the staging app server will be whatever latest version is on 
the repo in Nexus from the CI pipeline.


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


