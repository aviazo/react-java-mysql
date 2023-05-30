pipeline {   
  agent{      
    node { label 'docker'}     
  }  
  
  stages {  

    stage("Git Checkout"){           
      steps{                
	     checkout([$class: 'GitSCM', branches: [[name: '*/Dev']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-ssh', url: 'https://github.com/aviazo/react-java-mysql.git']]])           
           }        
        }
   
	  
	stage('Build and deploy') {
      steps {
             sh 'docker-compose build'
             sh 'docker-compose up -d'
            }
        }

    stage('Run tests') {
      steps {
             sh '''sleep 20'''
             sh '''curl -X GET http://127.0.0.1:3000'''
            }
        }
	  
    stage('Build Docker Image') {         
      steps {                
	         sh 'sudo docker build -t aviazo/react-java-mysql:$BUILD_NUMBER .'           
             echo 'Build Image Completed'                
            }           
       }  
    stage('Build and Push To Nexus Image') {
      steps {
             withCredentials([usernamePassword(credentialsId: 'nexus_user', passwordVariable: 'nexus_pass', usernameVariable: 'nexus_user')]) {
             sh '''docker tag aviazo/react-java-mysql:${BUILD_ID} 127.0.0.1:8083/react-java-mysql:${BUILD_ID}'''
             sh '''docker login 127.0.0.1:8083 -u $nexus_user -p $nexus_pass'''
             sh '''docker push 127.0.0.1:8083/react-java-mysql:${BUILD_ID}'''
	          }
 	        }
       	}

    stage('Cleanup') {
      steps {
             sh 'docker-compose down'
            }
        }
    }
}
