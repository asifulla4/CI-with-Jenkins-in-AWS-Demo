pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'asif-first-project'
                CLUSTER_NAME = 'kr-cluster-project'
                LOCATION = 'europe-north1-a'
                CREDENTIALS_ID = 'k8asifsvc'
		
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build') { 
                steps {
                  echo "Cleaning and packaging..."
                  sh 'mvn clean package'		
                }
           }
	   stage('Test') { 
		steps {
	          echo "Testing..."
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image') { 
		steps {
                   script {
                    myimage = docker.build("asifulla4/tomactimage:${env.BUILD_ID}")
		      /*myimage = docker.build("gcr.io/asifulla4/tomactimage:${env.BUILD_ID}")*/
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
                    /* docker.withRegistry('https://gcr.io', 'gcr-devops') {*/
                            myimage.push("${env.BUILD_ID}")		
                     }
			   
                   }
                }
            }
	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
          }
    }
}

