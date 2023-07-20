pipeline {
  agent any 
	environment{
			google_creds='gcloud-service-key'
  stages {
    stage('Login') {
      steps {
        sh "echo $DOCKERHUB_CREDENTIALS_USR"
        sh "echo $DOCKERHUB_CREDENTIALS_PSW"
        sh "$DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW"
      }
    }

    stage('Authenticate') {
      steps {
        sh '''
		  	gcloud auth activate-service-account --key-file="$google_creds"
		  '''
      }
    }

    stage('Auth') {
      steps {
        sh 'gcloud auth configure-docker'
      }
    }

    stage('Build') {
      steps {
        sh "docker build -t ${env.IMAGE_NAME} ."
      }
    }

    stage('Tag') {
      steps {
        sh 'docker tag hello gcr.io/nisarg-cludrun/hello'
      }
    }

    stage('push') {
      steps {
        sh "docker push gcr.io/nisarg-cludrun/${env.IMAGE_NAME}"
      }
    }

    stage('List') {
      steps {
        sh 'gcloud container images list --repository=gcr.io/nisarg-cludrun'
      }
    }

    stage('Deploy Cloud Run Service') {
      steps {
        script {
          withCredentials([file(credentialsId: 'gcloud-service-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
            sh "gcloud auth activate-service-account --key-file=${env.GOOGLE_APPLICATION_CREDENTIALS}"
            sh "gcloud config set project ${env.PROJECT_ID}"
            sh "gcloud config set run/region ${env.REGION}"
            sh "gcloud run deploy ${env.SERVICE_NAME} --image gcr.io/${env.PROJECT_ID}/${env.IMAGE_NAME} --port=${env.PORT} --platform managed"

          }
        }

      }
    }

    stage('Allow allUsers') {
      steps {
        sh '''
		  gcloud run services add-iam-policy-binding ${SERVICE_NAME}  --region=\'us-central1\' --member=\'allUsers\' --role=\'roles/run.invoker\'
		'''
      }
    }

  }
  environment {
    PROJECT_ID = 'nisarg-cludrun'
    IMAGE_NAME = 'hello'
    SERVICE_NAME = 'hello'
    REGION = 'us-central1'
    DOCKERHUB_CREDENTIALS = credentials('docker')
    GCLOUD_CREDS = credentials('gcloud-service-key')
    PORT = 5000
  }
}
