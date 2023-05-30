pipeline {   
  agent{      
    node { label 'docker'}     
  }  
  environment {     
    DOCKERHUB_CREDENTIALS= credentials('docker_hub-aviazo')     
  } 

  triggers {
            pollSCM '* * * * *'
           }   
  stages {  

    stage("Git Checkout"){           
      steps{                
	          git branch: 'dev', url: 'https://github.com/aviazo/react-java-mysql.git'             
	          echo 'Git Checkout Completed'            
           }        
    }
     stage('Build Docker Image') {         
      steps{                
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
