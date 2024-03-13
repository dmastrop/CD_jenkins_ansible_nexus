FROM tomcat:8-jre11
LABEL "Project"="Vprofile"
LABEL "Author"="dmastrop"


RUN rm -rf /usr/local/tomcat/webapps/*
#copy the artifact from source to the tomcat docker conatiner
COPY target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
WORKDIR /usr/local/tomcat/
VOLUME /usr/local/tomcat/webapps