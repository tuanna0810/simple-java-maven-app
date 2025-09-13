pipeline {
    agent any
//     agent {
//         docker {
//             image 'maven:3.8.1-openjdk-11'
//             args '-v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker --user root'
//         }
//     }
    tools {
        maven 'Maven'
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
            steps {
                echo 'Starting Docker Build & Push stage...'
                sh 'apt-get update && apt-get install -y docker.io'
                sh 'docker build -t xebeto/app1:${BUILD_NUMBER} .'
                sh 'docker tag xebeto/app1:${BUILD_NUMBER} xebeto/app1:latest'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push xebeto/app1:${BUILD_NUMBER}'
                    sh 'docker push xebeto/app1:latest'
                }
                echo 'Docker image pushed successfully'
            }
        }
    }
}
