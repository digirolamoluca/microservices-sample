pipeline {
  environment {
    registry = "digirolamo/${JOB_NAME}"   //non so se è digirolamo o digirolamoluca
    registryCredential = 'digirolamo-dockerhub'
    dockerImage = ''
    DOCKER_TAG = getVersion().trim()
    IMAGE="${JOB_NAME}"
  }
  
  
  //if is a nodejs app
  
  tools {
   nodejs 'NodeJS'
 }

 
  agent any
  stages {
    stage('SonarQube analysis'){
      steps{
        sh 'echo SonarQube analysis'
        
     
    withSonarQubeEnv('Sonarqube') { 
      // If you have configured more than one global server connection, you can specify its name
      sh "${tool("sonar_scanner")}/bin/sonar-scanner"
     
        } 
        
      }
    }
  
    stage('Building image') {
      steps{
        sh 'echo Building Image'
        
        script {
          sh 'docker build https://github.com/digirolamoluca/microservices-sample.git:Dockerfile'
          //dockerImage = docker.build("$registry:$DOCKER_TAG")
          //sh 'docker build -t digirolamo/microservices-sample:latest .'
        } 
      }
    }
    stage('Static Security Assesment'){
      steps{
        sh 'echo Static Security Assesment'
        /*
        sh 'docker run --name ${IMAGE} -t -d $registry:${DOCKER_TAG}'
        //Inserire il profilo che si vuole utilizzare, nel caso se ne vogliano utiilizzare più di uno aggiungere un'altra riga con un diverso nome del report
        sh 'inspec exec https://github.com/dev-sec/linux-baseline -t docker://${IMAGE} --reporter html:/Results/inspec_report.html --chef-license=accept || true'
        sh 'docker stop ${IMAGE}'
        sh 'docker container rm ${IMAGE}'
        
        sh 'git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/${JOB_NAME}.git"'
        sh 'git add Results/*'
        sh 'git commit -m "Add report File"'
        sh 'git push origin HEAD:main'
        */
     }
    }
    stage('Push Image') {
      steps{
        sh 'echo Push Image'
        /*
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
        */
      }
    }
 }
  post{
    success{
      echo 'Post success'
      build job: 'SecDevOpsFlowTemplate_microservices-sample', parameters: [string (value: "$registry"+":"+"$DOCKER_TAG", description: 'Parametro', name: '$IMAGE')]
    }
  }
}

def getVersion(){
  def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
  return commitHash
}
