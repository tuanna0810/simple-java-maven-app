pipeline {
    agent any
    tools {
        maven 'Maven'
        docker 'Docker'
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                echo 'Starting Deliver stage...'
                sh './jenkins/scripts/deliver.sh'
                echo 'Delivery completed'
            }
        }
        stage('Docker Build & Push') {
            agent {
                label 'docker'
            }
            steps {
                echo 'Starting Docker Build & Push stage...'
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        def image = docker.build("xebeto/app1:${env.BUILD_NUMBER}")
                        image.push()
                        image.push('latest')
                    }
                }
                echo 'Docker image pushed successfully'
            }
        }
    }
}
