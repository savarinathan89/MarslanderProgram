
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactoryCaseStudy"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
	
	rtMaven.tool = "maven"        
	rtMaven.deployer releaseRepo:'MarsLander', snapshotRepo:'MarsLander', server: server
    
 

    stage('Clone sources') {
	     
        git poll:true, url: 'https://github.com/savarinathan89/MarslanderProgram.git'
    }
	
    stage('SonarQube analysis') {
          withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
               
		          withMaven(maven:'maven') {
			           sh 'mvn ${SONAR_MAVEN_GOAL} -Dsonar.host.url=${SONAR_HOST_URL}'
                  }
          }
    }
   

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: ' clean install'
    }

  
    stage ('DeployToQA') {
                   sh 'curl -v -u jenkins:jenkins -T /var/lib/jenkins/workspace/MarsLander_Pipeline/target/JavaWebApp-1.0-SNAPSHOT.war "http://3.134.98.111:8080/manager/text/deploy?path=/QAWebapp&update=true"'
    }
	
    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
	
    stage ('functionalTesting'){
	     
	     rtMaven.run pom: 'functionaltest/pom.xml', goals: 'test'
	     //withMaven(maven:'maven') {
	     	//sh 'mvn -B -f /var/lib/jenkins/workspace/functional-testing/functionaltest/pom.xml  test'
	     //}
	} 
	
	stage ('Perf-Test'){
		blazeMeterTest credentialsId: 'BlazemeterCaseStudy', testId: '7912838.taurus', workspaceId: '468374'
	}
	
     
    stage ('DeployToProd'){
                   sh 'curl -v -u jenkins:jenkins -T /var/lib/jenkins/workspace/MarsLander_Pipeline/target/JavaWebApp-1.0-SNAPSHOT.war "http://18.191.247.103:8080/manager/text/deploy?path=/ProdWebapp&update=true"'
    }

    stage ('AcceptanceTest'){
	     
	     rtMaven.run pom: 'Acceptancetest/pom.xml', goals: 'test'
	     
	}
	
	stage("Slacknotification") {
                     slackSend channel: 'devops_case_study_team', color: '#BADA55', message: 'MarsLander deployment Completed', teamDomain: 'Sab-Devops-Learning', tokenCredentialId: 'slack1'
    }
     
}
	 
