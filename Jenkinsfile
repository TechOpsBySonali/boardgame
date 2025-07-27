pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TechOpsBySonali/boardgame.git'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Quality Check') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=boardgame \
                    -Dsonar.projectName=boardgame \
                    -Dsonar.java.binaries=target
                    '''
                }
            }
        }

        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Publish to Nexus') {
            steps {
                // Make sure 'maven-settings' exists in Global Settings in Jenkins
                withMaven(globalMavenSettingsConfig: 'maven-settings') {
                    sh 'mvn deploy'
                }
            }
        }
    }
}
