node{
    stage('SCM Checkout'){
      git 'https://github.com/PL-Arun/my-app.git'
    }
    stage('Maven Build'){
 
       def mvnHome =  tool name: 'maven3', type: 'maven'   
       sh "${mvnHome}/bin/mvn clean package"
       sh 'mv target/myweb*.war target/newapp.war'
    }
    stage('SonarQube Analysis') {
        def mvnHome =  tool name: 'maven3', type: 'maven'
        withSonarQubeEnv('Sonar') { 
          sh "${mvnHome}/bin/mvn sonar:sonar"
        }
    }
    stage('Build Docker Imager'){
       sh 'docker build -t arunpl/myweb:0.0.2 .'
       }
       stage('Docker Image Push'){
       withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
       sh "docker login -u arunpl -p ${dockerPassword}"
        }
       sh 'docker push arunpl/myweb:0.0.2'
       }
       stage('Nexus Image Push'){
          sh "docker login -u admin -p admin123 13.235.128.46:9795"
          sh "docker tag arunpl/myweb:0.0.2 13.235.128.46:9795/attack:0.0.1"
          sh 'docker push 13.235.128.46:9795/attack:0.0.1'
          }
       stage('Remove Previous Container'){
          try{
             sh 'docker rm -f tomcattest'
          }catch(error){
             //  do nothing if there is an exception
          }
          stage('Docker deployment'){
          sh 'docker run -d -p 8090:8080 --name tomcattest arunpl/myweb:0.0.2' 
          }
       }
 }
