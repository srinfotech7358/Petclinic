pipeline{
    
agent any

tools{

    maven 'Maven'
}

stages{
    stage('Clone The Project'){
        steps{
            git branch: 'main', url: 'https://github.com/srinfotechbatch2/Petclinic.git'
        }
    }
    
    stage('Build'){
        steps{
             bat 'mvn clean install'
        }
    }
     stage('Test'){
        steps{
             bat 'mvn test'
        }
    }
     stage('Generated the test reports'){
        steps{
             junit 'target/surefire-reports/*.xml'
        }
    }
     stage('published the Artifacts'){
        steps{
            archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
        }
    }

    stage('SonarQube Analysis'){
        steps{
        bat 'mvn package'
      bat '''mvn sonar:sonar \
  -Dsonar.projectKey=spring-petclinic \
  -Dsonar.projectName='spring-petclinic' \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_b4f05b06814df65b8d3f1a467f3ed604e2dadb03'''
        }
    }

stage ('Artifactory Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://localhost:8081/artifactory',
                 username: 'admin',
                  password: 'password',
                  bypassProxy: true,
                   timeout: 300
                        )
            }
        }
        
        stage('Upload'){
            steps{
                rtUpload (
                 serverId:"Artifactory" ,
                  spec: '''{
                   "files": [
                      {
                      "pattern": "*.war",
                      "target": "srinfotech-batch2"
                      }
                            ]
                           }''',
                        )
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "Artifactory"
                )
            }
        }


    
    stage('Deploy to Tomcat Server'){
        steps{
            deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'tomcatcredential', path: '', url: 'http://localhost:8080')], contextPath: 'SRInfotechSpringpetclinicJfrog', war: 'target/*.war'
        }
    }
}
}
