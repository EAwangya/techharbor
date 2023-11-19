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
        CENTRAL_REPO = 'eawangya-proxy'
        NEXUSIP = '192.168.56.18'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'eawangya-group'
        NEXUS_LOGIN = 'nexus-creds'

    }
    stages {
        stage ('Build') {
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }               
    }
}