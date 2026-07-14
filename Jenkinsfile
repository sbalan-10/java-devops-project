pipeline {

    agent any

    environment {
        IMAGE_NAME="ssivabalan/myapp:2"
        IMAGE_TAG="${BUILD_NUMBER}"
        DOCKER_CREDS=credentials('dockerhub-creds')
    }

    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/sbalan-10/java-devops-project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                sh '''
                echo $DOCKER_CREDS_PSW | docker login \
                -u $DOCKER_CREDS_USR \
                --password-stdin
                '''
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                docker push $IMAGE_NAME:$IMAGE_TAG

                docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest

                docker push $IMAGE_NAME:latest
                '''
            }
        }

      stage('Archive WAR') {
         steps {
              archiveArtifacts artifacts: 'target/*.war'
          }
       }

    }

    post {
        success {
            echo "Build Successful"
        }
        failure {
        echo "Build Failed"
       }
    }
}


