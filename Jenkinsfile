pipeline {
    agent any

    environment {
        PROJECT_ID = 'cryptic-album-384006'
        IMAGE_NAME = 'hello'
        SERVICE_NAME = 'hello'
        REGION = 'us-central1'
        DOCKERHUB_CREDENTIALS = credentials('docker')
    }

    stages {
        stage('Login') {
            steps {
                sh "echo $DOCKERHUB_CREDENTIALS_USR"
                sh "echo $DOCKERHUB_CREDENTIALS_PSW"
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW"
            }
        }

        stage('Build') {
            steps {
                sh '''
                    docker build -t moredatta574/${env.IMAGE_NAME} .
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                    docker push moredatta574/${env.IMAGE_NAME}
                '''
            }
        }

        stage('Tag') {
            steps {
                sh "docker tag moredatta574/${env.IMAGE_NAME} gcr.io/cryptic-album-384006/moredatta574/${env.IMAGE_NAME}"
            }
        }

        stage('List') {
            steps {
                sh "gcloud container images list --repository=gcr.io/cryptic-album-384006"
            }
        }

        stage('Deploy Cloud Run Service') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'gcloud-service-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        sh "gcloud auth activate-service-account --key-file=${env.GOOGLE_APPLICATION_CREDENTIALS}"
                        sh "gcloud config set project ${env.PROJECT_ID}"
                        sh "gcloud config set run/region ${env.REGION}"
                        sh "gcloud run deploy ${env.SERVICE_NAME} --image gcr.io/${env.PROJECT_ID}/${env.IMAGE_NAME} --platform managed"
                    }
                }
            }
        }
    }
}
