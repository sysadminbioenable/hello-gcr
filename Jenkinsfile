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
        stage('Build Docker Image') {
            steps {
                script {
                    sh "$DOCKERHUB_CREDENTIALS_USR"
                   sh "$DOCKERHUB_CREDENTIALS_PSW" 
				        sh "$DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR -p  $DOCKERHUB_CREDENTIALS_PSW"
                    }
                }
            }
        }
        
        stage('Deploy Cloud Run Service') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'gcloud-service-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
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
