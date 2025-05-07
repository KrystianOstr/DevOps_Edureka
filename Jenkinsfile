pipeline {
    agent { label 'worker' }

    tools {
        maven 'maven'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t mrbadd/abc-retail-app:${BUILD_NUMBER} .'
                sh 'docker tag mrbadd/abc-retail-app:${BUILD_NUMBER} mrbadd/abc-retail-app:latest'
            }
        }

        stage('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push mrbadd/abc-retail-app:${BUILD_NUMBER}'
                sh 'docker push mrbadd/abc-retail-app:latest'
            }
        }

        stage('Docker Run') {
            steps {
                sh '''
                    docker stop retail-app || true
                    docker rm retail-app || true
                    docker run -d -p 8081:8080 --name retail-app mrbadd/abc-retail-app:latest
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}

