pipeline {
    agent any
    tools {
       maven 'Maven3.9'
      }
    stages {
        stage ('codecheckout') {
            steps{
              checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ankvishwakarma/Mavenbuild.git',credentialsId: 'ankgit']])
            }
        }
        stage ('Build') {
            steps{
                 sh """
			        mvn clean install
			        mvn dependency:copy-dependencies
			        ls -lart target
	     	        """
	     	     archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
               }
            }
        stage ('SonarAnalysis') {
            steps{
              withSonarQubeEnv(installationName: 'sonar') {
               sh "mvn clean verify sonar:sonar -Dsonar.projectKey=checkcodecoverage -Dsonar.projectName='checkcodecoverage'"
               }
            }
        }
        stage ('Deploy') {
            steps{
                   sh """
                              echo 'Starting deployment sequence...'
                              # Clear target and copy file using absolute paths
                              rm -f /root/jenkins-data/tomcatwar/java-example.war
                              cp -p ${env.WORKSPACE}/target/java-example.war /var/tomcatwar/
	     	        """
            }
        }    
    }
}
