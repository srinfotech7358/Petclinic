
pipeline{

    tools{


        maven 'Maven'
    }
agent any

stages{

    stage('Clone'){

        steps{

            git branch: 'release/2025.06.04', url: 'https://github.com/srinfotech7358/Petclinic.git'
        }
    }

     stage('Build') {
            steps {
               bat 'mvn clean install'
            }
        }

         stage('Test Cases') {
            steps {
               bat 'mvn test'
            }
        }
 stage('package') {
            steps {
               bat 'mvn package'
            }
        }

         stage('Archive the Artifacts') {
            steps {
               archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
            }
        }
 stage('Sonarqube Analysis') {
            steps {
               
               bat 'mvn package'
              bat '''mvn sonar:sonar \
             -Dsonar.projectKey=spring-petclinic \
             -Dsonar.projectName='spring-petclinic' \
            -Dsonar.host.url=http://localhost:9000 \
            -Dsonar.token=sqp_96cf5222ab632b69c14baa5590210a7125185d5a'''
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
              "target": "srinfotech-solutions-private-limited"
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
 stage('Deploy Application into Tomcat Server') {
            steps {
               deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'NewTomcat', path: '', url: 'http://localhost:8080/')], contextPath: 'SRIN solutions PVT LTD', war: '**/*.war'
            }
        }

}

}