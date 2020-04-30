node {
   def commit_id
   try {


   
      stage('Preparation') {
        checkout scm
        sh "git rev-parse --short HEAD > .git/commit-id"                        
        commit_id = readFile('.git/commit-id').trim()
      }

      stage('test') {
        nodejs(nodeJSInstallationName: 'nodejs') {
          sh 'npm install --only=dev'
          sh 'npm test'
        }
      }

      stage('docker build/push') {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
          def app = docker.build("adenijiazeez/docker-nodejs-demo:${commit_id}", '.').push()
          // send slack notification
          slackSend (color: '#00FF00', message: "BUILD SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
      }
   }

   catch(e) {
    // mark build as failed
    currentBuild.result = "FAILURE";

    // send slack notification
    slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

    // throw the error
    throw e;
  }


}
