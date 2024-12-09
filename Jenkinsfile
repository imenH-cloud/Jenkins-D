pipeline {
    agent any

    environment {
        registry = "imenH-cloud/Jenkins-D"
        registryCredential = 'docker-hub-credentials'
        dockerImage = ''
    }

    triggers {
        pollSCM('*/5 * * * *')
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/imenH-cloud/Jenkins-D.git'
            }
        }

        stage('Build App') {
            steps {
                sh 'chmod +x ./mvnw'
                sh './mvnw clean package'
            }
        }

        stage('Initialize Docker') {
            steps {
                script {
                    def dockerHome = tool 'MyDocker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    docker.image(dockerImage).run('--name demo-jenkins -d -p 2222:2222')
                }
                slackSend color: "good", message: "${registry}:${BUILD_NUMBER} - image successfully created! :man_dancing:"
            }
        }
    }

    post {
        success {
            echo 'Pipeline execution successful!'
            slackSend color: "good", message: "Pipeline execution successful! :man_dancing:"
        }
        failure {
            echo 'Pipeline execution failed.'
            slackSend color: "danger", message: "Pipeline execution failed! :ghost:"
        }
    }
}
