pipeline {
    agent any 
    tools {
        
        maven 'maven3'
        jdk 'OracleJDK8'
    }
    environment {
        SNAP_REPO = 'eawangya-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin123'
        RELEASE_REPO = 'eawangya-release'
	    CENTRAL_REPO    = 'eawangya-maven-central'
        NEXUSIP = '192.168.56.18'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'eawangya-group'
        NEXUS_LOGIN = 'nexus-creds'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'

    }
    stages {
        stage ('Build') {
            steps {
                sh 'mvn -s settings.xml -DskipTests clean install'
                // sh 'mvn clean install -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Drepository.username=admin -Drepository.password=admin123'
            }
        }               
    }
}