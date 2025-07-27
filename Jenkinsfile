pipeline {
    agent any
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/raghu-solarch/boardgame.git'
            }
        }
        
        stage('Test') {
            steps {
               sh 'mvn test'
            }
        }
        
        
        stage('Quality Check') {
            steps {
                timeout(time: 60, unit: 'SECONDS') {
                    withSonarQubeEnv('sonar-server') {
                        sh '''
                        $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=boardgame -Dsonar.projectKey=boardgame \
                        -Dsonar.java.binaries=.
                        '''
                    }
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
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: '', maven: '', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy'
                }               
            }
        }
    }
}
