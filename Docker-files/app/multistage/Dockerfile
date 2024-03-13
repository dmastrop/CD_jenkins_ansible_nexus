# multistage docker file.  Note using JDK11 and not JDK8
# Note that this Dockerfile is going to be run on the Jenkins server (we installed docker engine on the Jenkins server)
# It will be run in the stage('Build App Image') in the Jenkinsfile for StagePipeline (not ProdPipeine).  This stage will build
# the docker image as instructed below with ECR appRegistry and BUILD_NUMBER as a tag and a subsequent stage will upload this image
# to AWS ECR with the registry credentials that are stored in Jenkins (this is an IAM AWS user token and secret)

# Stage1: first container used build the vprofile app (.war)
FROM openjdk:11 AS BUILD_IMAGE
RUN apt update && apt install maven -y
RUN git clone https://github.com/devopshydclub/vprofile-project.git
# in realtime would copy own local source code into the image and run mvn install: can use a volume, COPY command or ADD option
# Here we will just clone the source code for vprofile app in the image.  Using devopshydclub here instead of hkhcoder
RUN cd vprofile-project && git checkout docker && mvn install
# cd into the vprofile-project and checkout the docker branch and run mvn install to build the vprofile app 



# Stage2: copy the vprofile artifact .war file from Stage1 container to Stage2 which is the tomcat docker container image.
# This is the image that we will upload to ECR
FROM tomcat:9-jre11

RUN rm -rf /usr/local/tomcat/webapps/*
# Remove default path

COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
# Replace with the vprofile-v2.war as default application on tomcat server

EXPOSE 8080
CMD ["catalina.sh", "run"]
# catalina.sh run starts tomcat in the foreground, displaying the logs on the console that you started it. 
# The tomact server will have the vprofile running as default app on port 8080.
