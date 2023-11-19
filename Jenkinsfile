pipeline {
    agent any 
    tools {
        maven: "MAVEN3"
        jdk: "OracleJDK8"
    }
    environment {
        SNAP_REPO = 'techharbor-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin123'
        RELEASE_REPO = 'techharbor-release'
        CENTRAL_REPO = 'techharbor-central'
        NEXUSIP = '192.168.56.18'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'techharbor-group'
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