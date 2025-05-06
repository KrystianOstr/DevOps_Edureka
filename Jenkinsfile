pipeline {
    agent {label 'worker'}

    stages {

        // TASK 2

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

        // TASK 3

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t abc-retail-app .'
            }
        }

        stage('Run Docker Container') {
            steps {

                // Stop and remove any existing container named 'retail-app' to avoid conflicts.
                // The '|| true' ensures the pipeline doesn't fail if the container doesn't exist.
                sh '''
                    docker stop retail-app || true
                    docker rm retail-app || true
                    docker run -d -p 8081:8080 --name retail-app abc-retail-app
                '''
            }
        }


    }
}

