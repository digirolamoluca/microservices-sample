
pipeline {
  environment {
    registry = "digirolamo/${JOB_NAME}"   
    registryCredential = 'dockerhub_credentials'
    dockerImage = ''
    DOCKER_TAG = getVersion().trim()
    IMAGE="${JOB_NAME}"

  }
  

  
  //if is a nodejs app
  
 // tools {
//   nodejs 'NodeJS'
// }

 //tutti gli agenti sono gestiti da un controller jenkins che rappresenta la nostra macchina su cui è locato jenkins
  
  agent any  
  stages {
 
    
    //prima: ./sonar.sh start
    stage('SonarQube analysis'){
      steps{
        sh 'echo SonarQube analysis'
        
     
    withSonarQubeEnv('Sonarqube') { 
   //nelle parentesi tool va il nome che stato inserito nella configurazione di sonarscanner tool su jenkins  
      sh "${tool("Sonarqube")}/bin/sonar-scanner"
     
        }         
      }
    }

    
    
    stage('Building image') {
      steps{
        sh 'echo Building Image'
        
        script {
           dockerImage = docker.build("$registry:$DOCKER_TAG")
        } 
      }
    }
    stage('Static Security Assesment'){
      steps{
        sh 'echo Static Security Assesment'
        
        
        
        sh 'docker run --name ${IMAGE} -t -d $registry:${DOCKER_TAG}'
        //sh 'docker run --name rabbitmq -t -d rabbitmq'
        
        //-d : 	Esegui contenitore in background e stampa ID contenitore
        //-t :  Assegno una pseudo-TTY per eventuali operazioni future interne al container
        
         withCredentials([usernamePassword(credentialsId: 'jenkins', passwordVariable: '123456789', usernameVariable: 'jenkins')]) {  //$
      
        //Inserire il profilo che si vuole utilizzare, nel caso se ne vogliano utiilizzare più di uno aggiungere un'altra riga con un diverso nome del report
     
           //sh 'inspec exec https://github.com/dev-sec/example -t docker://rabbitmq --reporter html:Results/RabbitMQ_example_report.html --chef-license=accept || true'
           sh 'inspec exec https://github.com/dev-sec/linux-baseline -t docker://${IMAGE} --reporter html:Results/Linux_report.html --chef-license=accept || true'
           sh 'inspec exec https://github.com/dev-sec/apache-baseline -t docker://${IMAGE} --reporter html:Results/Apache_report.html --chef-license=accept || true'   
          sh 'docker stop ${IMAGE}'
          sh 'docker container rm ${IMAGE}'

          // sh 'cp /var/lib/jenkins/workspace/microservice-sample/Results/* /home/digirolamo/Desktop'
         }}
    } //$
   
    
    
 /*   stage('Public on git Report inspec'){ 
      steps{  
        //PER GIT DI QUANTO SEGUE CONFIGURARE UNA COPPIA DI CHIAVI SSH E SETTARE PERSONAL ACCESS TOKEN 
        
        script {
          //def TOKEN = readFile(file: 'token.txt')
          //println(TOKEN)       
               
         withCredentials([usernamePassword(credentialsId: 'credentialgithub', passwordVariable: 'gittabbodege9', usernameVariable: 'digirolamoluca')]) {        
          
       
          sh 'cd /var/lib/jenkins/workspace/microservice-sample/Results'
          sh 'git add *'
          sh 'git commit -m "Add report"'
          sh 'sudo su | cd'
          
          sh 'git pull origin master'
          sh 'git push https://digirolamoluca:gittabbodege9@github.com/digirolamoluca/microservices-sample.git HEAD:master'
          }
          
          
           withCredentials([usernamePassword(credentialsId: 'credentialgithub', passwordVariable: 'gittabbodege9', usernameVariable: 'digirolamoluca')]) {        
          
             
             
          sh 'git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/${JOB_NAME}.git"'
          sh 'git config --global user.email "lucadigirolamo@hotmail.it"'
          sh 'git config --global user.name "Luca"' 
          sh 'sudo su | cd'
          sh 'cd /var/lib/jenkins/workspace/microservice-sample'
             //o meglio  sh 'cd /var/lib/jenkins/workspace/${JOB_NAME}'    siccome questa è il progetto che jenkins crea localmente
          sh 'git add Results/*'
          sh 'git commit -m "Add report File"'
          sh 'git push origin HEAD:master'
          
        } 
          
        }
        }
        }  */
        
  
 
    
    
    stage('Push Image') {
      steps{
        sh 'echo Push Image'
        
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
        
      }
    }
 }
/*  post{
    success{
      echo 'Post success'
      build job: 'SecDevOpsFlowTemplate_microservices-sample', parameters: [string (value: "$registry"+":"+"$DOCKER_TAG", description: 'Parametro', name: '$IMAGE')]
    }
  } */
}

def getVersion(){
  def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
  return commitHash
}
 
