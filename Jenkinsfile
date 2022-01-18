node{
   stage('SCM Checkout'){
     git 'https://github.com/Sathish-Smith/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   
   stage('Build Docker Imager'){
   sh 'docker build -t sathishsmith/myweb:0.0.2 .'
   }
   
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'Jenkins', variable: 'passwordText')]) {
   sh "docker login -u sathishsmith -p ${passwordText}"
    }
   sh 'docker push sathishsmith/myweb:0.0.2'
   }
   
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.232.133.79:8003"
   sh "docker tag sathishsmith/myweb:0.0.2 13.232.133.79:8003/smith:1.0.0"
   sh 'docker push 13.232.133.79:8003/smith:1.0.0'
   }	

   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest5'
	}catch(error){
		//  do nothing if there is an exception
	}
   
   stage('Docker deployment'){
   sh 'docker run -d -p 8030:8080 --name tomcattest5 sathishsmith/myweb:0.0.2' 
   }
  }
}
