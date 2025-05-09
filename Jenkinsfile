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

        stage('Deploy with Ansible') {
            steps {
                script {
                    sshagent(credentials: ['ansible-ssh-key']) {
                        withCredentials([
                            usernamePassword(
                                credentialsId: 'dockerhub-creds',
                                usernameVariable: 'USERNAME',
                                passwordVariable: 'PASSWORD'
                            )
                        ]) {
                            sh """
                                /usr/bin/ansible-playbook docker-deploy.yaml -i inventory \
                                -e build_number=${BUILD_NUMBER} \
                                -e docker_username=$USERNAME \
                                -e docker_password=$PASSWORD
                            """
                        }
                    }
                }
            }
        }
    }
}

