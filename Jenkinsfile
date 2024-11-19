pipeline {
    agent any
    tools {
        maven "MAVEN3.9"
        jdk "JDK17"
    }

    stages {
        stage('Fetch code') {
            steps{
                git branch: 'atom', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        stage('Build'){
            steps{
                sh 'mvn install -DskipTests'
            }
            post{
                success{
                    echo "Archiving artifact"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Unit Test') {
            steps{
                sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage("SonarQube analysis") {
            environment {
                scannerHome = tool 'sonar6.2'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
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
        stage("Quality Gate"){
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }    
        }
        stage("Upload Arfitaft"){
            steps{
                nexusArtifactUploader(
                nexusVersion: 'nexus3',
                protocol: 'http',
                nexusUrl: '172.31.86.104:8081',
                groupId: 'QA',
                version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                repository: 'vprofile-repo',
                credentialsId: 'nexuslogin',
                artifacts: [
                    [artifactId: 'vproapp',
                    classifier: '',
                    file: 'target/vprofile-v2.war',
                    type: 'war']
            
                ])
            }  
        }
    }
}