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
        stage('Tomcat Deploy'){
            steps{
                script{
                    def userHost = "ec2-user@172.31.32.127"
                    def tomcatBin = "ec2-user@172.31.32.127 /opt/tomcat8/bin"
                    sshagent(['tomcat-dev']) {
                        // copy war file to tomcat webapps
                        sh "scp -o StrictHostKeyChecking=no target/*.war ${userHost}:/opt/tomcat8/webapps/TCSproject.war"
                        // start and stop tomcat
                        sh "ssh ${tomcatBin}/shutdown.sh"
                        sh "ssh ${tomcatBin}/startup.sh"
                    }
                }
            }
        }
    }
}
