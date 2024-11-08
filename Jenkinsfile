pipeline {
    agent any
    tools {
        maven "MAVEN 3.9"
        jdk "JDK17"
    }

    stages {
        stage('Fetch code') {
            steps{
                git branch: 'atom', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        stage('Unit Test') {
            steps{
                sh 'mvn test'
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
    }
}