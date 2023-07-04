pipeline {
    agent any
    
    environment {
        PROJECT_ID = 'cryptic-album-384006'
        IMAGE_NAME = 'hello'
        SERVICE_NAME = 'hello'
        REGION = 'us-central1'
        #IMAGE_TAG = 'latest' // or specify a different tag if needed
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://gcr.io', 'gcr-credentials') {
                        sh "docker build -t gcr.io/${env.PROJECT_ID}/${env.IMAGE_NAME} ."
                        sh "docker push gcr.io/${env.PROJECT_ID}/${env.IMAGE_NAME}"
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
