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
            post {
                success {
                    echo "Now Archiving..."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn -s settings.xml test'
            }
        }
        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }
        stage('SonarQube Analysis') {
            environment {
                def scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSCANNER}") {
                sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=eawangya \
                    -Dsonar.projectName=eawangya \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }        
    }
}
