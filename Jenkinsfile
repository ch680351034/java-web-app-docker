node {

    def dockerImage = 'ch680351034/appone'
    def dockerTag = 'latest'
    def dockerCredentialId = 'dockerhub'

    stage('clone repositary') {
        git 'https://github.com/ch680351034/java-web-app-docker.git'
    }

    stage('maven build') {
        docker.image('maven:3.8.1-openjdk-11').inside {
         sh 'pwd && ls -l '
         sh "mvn clean package"
         echo "build has been done"
         sh 'pwd && ls -l '

        }
        //  def mvnHome = tool name: 'maven399', type: 'maven'
        //  env.PATH = "${mvnHome}/bin:${env.PATH}"

    }

    stage('Push to Docker Hub') {

        docker.image('docker:latest').inside {

        withDockerRegistry(credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/') {

        def app = docker.build("${dockerImage}:${dockerTag}")
        app.push()              // Push with the default tag (e.g., 'latest')
        app.push("${env.BUILD_NUMBER}")  // Push with the build number as the tag
        
        }

        }

      }
    
    stage('archive artifacts') {
         archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
    }

    stage('deploy to prod enviorment ') {

         if (env.BRANCH_NAME == 'master') {
          sh 'docker pull ch680351034/appone:latest'
          sh 'docker rm -f prod-appone || true'
          sh 'docker run -d --name prod-appone -p 9090:8080 ch680351034/appone:latest'

         }
    
    }

   
   stage('deploy to dev enviorment ') {

         
         if (env.BRANCH_NAME.startsWith('feature/')) {
          sh 'docker pull ch680351034/appone:latest'
          sh 'docker rm -f dev-appone || true'
          sh 'docker run -d --name dev-appone -p 9091:8080 ch680351034/appone:latest'    
             
         }

    }

    
    
}
