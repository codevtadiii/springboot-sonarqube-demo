pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/codevtadiii/springboot-sonarqube-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                        sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=springboot-demo \
                        -Dsonar.token=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t springboot-sonarqube-demo .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop springboot-app || true
                docker rm springboot-app || true

                docker run -d \
                  --name springboot-app \
                  -p 8085:8080 \
                  springboot-sonarqube-demo
                '''
            }
        }
    }
}
