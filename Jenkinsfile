pipeline {
environment {
registry = 'digirolamo/test'
registryCredential = 'dockerhub_credentials'
dockerImage = ''
DOCKER_TAG = getVersion().trim()
IMAGE = 'test'
}

agent any
 stages { 

 stage('SonarQube analysis'){
  steps{
   sh 'echo SonarQube analysis'
 withSonarQubeEnv('Sonarqube') { 
  sh '${tool('Sonarqube')}/bin/sonar-scanner'
}}}


 stage('Building image') {
  steps{
   sh 'echo Building Image'
   script {
     dockerImage = docker.build('$registry:$DOCKER_TAG')
}}}


 stage('Static Security Assesment'){
  steps{
   sh 'echo Static Security Assesment'
   sh 'docker run --name ${IMAGE} -t -d $registry:${DOCKER_TAG}'
 withCredentials([usernamePassword(credentialsId: 'jenkins', passwordVariable: '123456789', usernameVariable: 'jenkins')]) {
 //inserire qui gli stig da definire in basde al tipo di sistema. Inseriamo 2 stig di esempio
 sh 'inspec exec https://github.com/dev-sec/linux-baseline -t docker://${IMAGE} --reporter html:Results/Linux_report.html --chef-license=accept || true'
 sh 'inspec exec https://github.com/dev-sec/apache-baseline -t docker://${IMAGE} --reporter html:Results/Apache_report.html --chef-license=accept || true'
 sh 'docker stop ${IMAGE}'
}}}

 stage('Push Image') {
  steps{
   sh 'echo Push Image'
   script {
    docker.withRegistry( '', registryCredential ) {
      dockerImage.push()
 }}}}}}
def getVersion(){
 def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
 return commitHash
}
