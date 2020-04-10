
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactoryCaseStudy"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    
 rtMaven.tool = "maven"

    stage('Clone sources') {
        git url: 'https://github.com/savarinathan89/MarslanderProgram.git'
    }
    stage('SonarQube analysis') {
          withSonarQubeEnv(credentialsId: 'sonartoken', installationName: 'sonarqube') 
	  { // You can override the credential to be used
               
		  withMaven(maven:'maven') {
                        sh 'mvn -Dsonar.login=admin -Dsonar.password=admin sonar:sonar '
                    }
          }
    }
    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        //rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        //rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
	rtMaven.deployer releaseRepo:'MarsLander', snapshotRepo:'MarsLander', server: server
        //rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
    }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
    }
	 
