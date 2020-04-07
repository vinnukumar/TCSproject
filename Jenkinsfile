pipeline{
    agent any
    tools {
        maven 'maven3'
    }

    stages{
		stage('Maven Build/Package'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Nexus Deploy'){
            steps{
                script{
                    def pomFile = readMavenPom file: 'pom.xml'
                    def version = pomFile.version
                    def nexusRepo = version.endsWith ("SNAPSHOT") ? "TCSproject-snapshots" : "TCSproject-release"
                    nexusArtifactUploader artifacts: [[artifactId: 'TCSproject', classifier: '', file: 'target/TCSproject.war', type: 'war']], 
                        credentialsId: 'nexus3', 
                        groupId: 'Bangalore', 
                        nexusUrl: '172.31.39.75:8081', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: 'TCSproject-snapshots', 
                        version: version
                }
            }
        }
    }
}
