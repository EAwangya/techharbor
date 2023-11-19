def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
    'ABORTED': 'warning',
    'UNSTABLE': 'warning',
    'NOT_BUILT': 'gray', // or any other color you prefer
    'UNKNOWN': 'gray',   
]
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
        registry = "eawangya/techharbor"
        registryCredential = 'dockerhub-creds' 


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
                withSonarQubeEnv("${SONARSERVER}") {
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
        stage("Quality Gate"){
            steps {
                timeout(time: 60, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true

                }
            }
        }
        stage("UploadArtifacct to Nexus"){
            steps{
                  nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                        groupId: 'QA',
                        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                        repository: "${RELEASE_REPO}",
                        credentialsId: "${NEXUS_LOGIN}",
                        artifacts: [
                            [artifactId: 'eawangyaeapp',
                            classifier: '',
                            file: 'target/eawangya-v1.war',
                            type: 'war']
                    ]
               )  
            }
        }
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy Image') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                }
            }
        }
    }               
}
    post {
        always {
            echo 'Slack Notification.'
            slackSend channel: '#devops-cicd-pipeline',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NANE} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
