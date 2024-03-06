pipeline {
    agent any
    //run directly on Jenkins server without any agent for now
    // test commit5
    tools {
        maven "MAVEN3_9"
        // there are 2 maven versions in tools. One installed via ssh terminal is MAVEN3_6
        // MAVEN3_9 is auto installed from the Java Tools configuration
        jdk "OracleJDK11"
        // JDK8 is deprecated. Do not use it. 
    }

    // the environment block is used to specify the variable values that are referenced in the pom.xml and settings.xml
    // the pom.xml will be used by maven to build from the source. The settings will define the Nexus repo URL that the
    // pom.xml needs to access the central reposistory for the dependencies.....
    environment {
        //Nexus server environment:
        // these values were defined during Nexus server setup
        // note that underscore for variable names instead of dash. Dash is illegal character for Jenkinsfile.
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin'
        //NEXUS_PASSWORD = 'admin'
        //NEXUS_USER = "admin"
        //NEXUS_PASSWORD = "admin"
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        // note that NEXUSIP is the private IP of the Nexus EC2 instance. This does not change even after reboots,
        // unlike public IP.  Jenkins server and Nexus server are in the same AWS VPC and in same subnet
        NEXUSIP = '172.31.28.191'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        // the NEXUS_LOGIN was defined in the Jenkins credentials settings. This will be used later.
        NEXUS_LOGIN = 'nexuslogin'

        //sonarqube environment:
        // Manage Jenkins System: this references the server ip, token, etc....
        SONARSERVER = 'sonarserver'
        // Manage Jenkins Tools: this references the scanner version, etc....
        SONARSCANNER = 'sonarscanner'


    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
                // -DskipTests so it will not run any tests. It will run mvn install.  The settings.xml will be
                // passed to it as parameters.  Settings.xml is in this same workspace 
                // We want Jenkins to download dependencies from Nexus. That is specified in the pom.xml file in 
                // this workspace.  The Nexus repo is specified at the  end of the pom.xml
            }

            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                    //archiveArtifacts is a pliugin.  This states to archive everyting that ends in .war.
                    //  our .war file is in the workspace for the particular jenkins pipeline
                    // in the terminal is is under /var/lib/jenkins/workspace/vprofile-hkhcoder-ci-pipeline2/target
                }
            }
        }

        stage('Test'){
            steps {
                //sh 'mvn test'
                sh 'mvn -s settings.xml test'
                // this will generate a test. Later on we will configure this to place it on Sonarqube
                // note if do not add -s settings.xml then dependencies will be downloaded from maven instead
                // of Nexus repo.
            }
        }    

        stage('Checkstyle Analysis'){
            steps {
                //sh 'mvn checkstyle:checkstyple'
                sh 'mvn -s settings.xml checkstyle:checkstyle'
                // uses checkstyple code analysis and suggest best practices and vulnerabilities.
                // note if do not add -s settings.xml then dependencies will be downloaded from maven instead
                //of Nexus repo.
            }
        }

        stage('SonarAnalysis') {
            environment {   
                scannerHome = tool "${SONARSCANNER}"
                // scannerHome is used as a local varaible here witin this stage. See below.
            }
            steps {
                // the source directory src is the directory that it will scan
                // project key is vprofile, project name is vprofile (This will appear in the sonarqube server)
                // all of these paths and files are in the source code in the github repo
                withSonarQubeEnv("${SONARSERVER}") {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
            }
        }
    

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // timeout after 1 hour if the plugin below does not respond

                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Quality Gate for this vprofile project is vprofileQG
                    // the sonarqubetojenkins webhook is http://172.31.25.39:8080/sonarqube-webhook and has been
                    // atached to the vprofile project in sonarqube.
                    waitForQualityGate abortPipeline: true
                }
            }
        }

    // stages block end
    }
    
// pipeline block end
} 