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
                // Add the chmod command here
                sh 'chmod +x ./mvnw'
                // Execute the Maven build
                sh './mvnw clean package'
            }
        }

        stage('Build image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Deploy Docker container') {
            steps {
                sh "docker run --name demo-jenkins -d -p 2222:2222 $registry:$BUILD_NUMBER"
                slackSend color: "good", message: registry + ":$BUILD_NUMBER" + " - image successfully created! :man_dancing:"
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

