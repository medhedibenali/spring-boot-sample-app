pipeline {
    environment {
        registry = 'medhedibenali/spring-boot-sample-app'
        registryCredential = 'docker_hub_id'
        dockerImage = ''
    }

    agent any

    tools {
        maven "Default"
    }

    stages {
        stage('Clone the repository') {
            steps {
                git url: 'https://github.com/medhedibenali/spring-boot-sample-app.git', branch: 'main'
            }
        }

        stage('Build and test the app') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build the image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Push the image') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Update the latest tag') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push 'latest'
                    }
                }
            }
        }

        stage('Clean up') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
    }

    post {
        success {
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.jar'
        }
    }
}
