pipeline {
    agent any

    environment {
        PROJECT_ID = 'nisarg-cludrun
        IMAGE_NAME = 'hello'
        SERVICE_NAME = 'hello'
        REGION = 'us-central1'
        DOCKERHUB_CREDENTIALS = credentials('docker')
	GCLOUD_CREDS=credentials('gcloud-service-key')
        PORT=5000
    }

    stages {
        stage('Login') {
            steps {
               bat "cmd /c echo $DOCKERHUB_CREDENTIALS_USR"
              # bat "cmd /c echo $DOCKERHUB_CREDENTIALS_PSW"
               bat "cmd /c echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW"
            }
        }

       stage('Authenticate') {
	      steps {  
		   bat """ cmd /c
		  	gcloud auth activate-service-account --key-file="$GCLOUD_CREDS"
		  """
		   
	              }
	        }
	 stage('Auth') {
	      steps {  
		  
		  	 bat "cmd /c gcloud auth configure-docker"
		 
		   
	              }
	        }    

        stage('Build') {
            steps {
                
                 bat "cmd /c docker build -t ${env.IMAGE_NAME} ."
                
            }
        }

        stage('Tag') {
            steps {
                bat "cmd /c docker tag moredatta574/${env.IMAGE_NAME} gcr.io/nisarg-cludrun/${env.IMAGE_NAME}"
            }
        }

        stage('push') {
            steps {
                 bat "cmd /c docker push gcr.io/nisarg-cludrun/${env.IMAGE_NAME}"
            }
        }

        stage('List') {
            steps {
                bat "cmd /c gcloud container images list --repository=gcr.io/nisarg-cludrun"
            }
        }

        stage('Deploy Cloud Run Service') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'gcloud-service-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                      bat "cmd /c gcloud auth activate-service-account --key-file=${env.GOOGLE_APPLICATION_CREDENTIALS}"
                         bat "cmd /c gcloud config set project ${env.PROJECT_ID}"
                         bat "cmd /c gcloud config set run/region ${env.REGION}"
                         bat "cmd /c gcloud run deploy ${env.SERVICE_NAME} --image gcr.io/${env.PROJECT_ID}/${env.IMAGE_NAME} --port=${env.PORT} --platform managed"
		
                    }
                }
            }
        }
	stage('Allow allUsers') {
	      steps {
		bat  """ cmd /c
		  gcloud run services add-iam-policy-binding ${SERVICE_NAME}  --region='us-central1' --member='allUsers' --role='roles/run.invoker'
		"""
	      }
	    }    
    }
}
