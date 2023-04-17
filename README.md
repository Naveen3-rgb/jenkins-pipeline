# jenkins-pipeline
sonarqube test

pipeline {
    agent any
    tools {
        maven 'maven 3.6.0'
    }
    stages {
        stage('Checkout Git') {
            steps {
                git branch: 'main', url: 'https://github.com/'
            }
        }
        stage('Build & JUnit Test') {
            steps {
                sh 'mvn clean install'
            }
            post {
                success {
                    junit 'target/surefire-reports/**/*.xml'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonarqube') {
                    sh "mvn sonar:sonar \
                        -Dsonar.projectKey=projects key \
                        -Dsonar.host.url=http://your port:9000 \
                        -Dsonar.login="token"
                }
            }
        }
        stage('Publish Results to SonarQube') {
            steps {
                withSonarQubePublisher()
            }
        }
    }
}
