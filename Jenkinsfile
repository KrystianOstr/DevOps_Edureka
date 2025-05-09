pipeline {
    agent any

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

        stage('Deploy to Kubernetes via Ansible') {
            steps {
                script {
                    sshagent(credentials: ['ansible-ssh-key']) {
                        withCredentials([usernamePassword(
                            credentialsId: 'dockerhub-creds',
                            usernameVariable: 'USERNAME',
                            passwordVariable: 'PASSWORD'
                        )]) {
                            sh """
                                export PATH=$PATH:/usr/bin
                                ansible-playbook k8s-deploy2.yaml -i inventory \
                                -e docker_username=$USERNAME \
                                -e docker_password=$PASSWORD \
                                -e build_number=${BUILD_NUMBER}
                            """
                        }
                    }
                }
            }
        }
    }
}
