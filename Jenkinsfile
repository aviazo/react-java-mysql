pipeline {   
  agent{      
    node { label 'docker'}     
  }  
  
  stages {  
    
    stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }	  

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
             script {

                        sh '''cp /slave/workspace/react-java-mysql/frontend/package.json /slave/workspace/react-java-mysql/package.json'''
                        sh '''pwd'''
                        sh '''mkdir -p /slave/workspace/react-java-mysql/public/ && cp -r /slave/workspace/react-java-mysql/frontend/public/index.html /slave/workspace/react-java-mysql/public/index.html'''
                        sh '''npm install'''
                        sh '''pwd'''
                        sh '''docker compose up -d'''
        		sh '''docker compose push'''
		        sh 'docker tag 127.0.0.1/slave/react-java-mysql:$BUILD_NUMBER .'           
                        echo 'Build Image Completed'                
                }           
            }        
        }  
    stage('Build and Push To Nexus Image') {
      steps {
             withCredentials([usernamePassword(credentialsId: 'nexus', passwordVariable: 'nexus_pass', usernameVariable: 'nexus_user')]) {
             sh '''docker tag aviazo/react-java-mysql:${BUILD_ID} 127.0.0.1:8083/react-java-mysql:${BUILD_ID}'''
             sh '''docker login 127.0.0.1:8083 -u $nexus -p $nexus_pass'''
             sh '''docker push 127.0.0.1:8083/react-java-mysql:${BUILD_ID}'''
	          }
 	        }
       	}

   /* stage('Cleanup') {
      steps {
             sh 'docker-compose down'
            }
        }*/
    }
 
}
