pipeline {
  agent any
  
  stages {
    stage('Checkout GitHub Repo') {
      steps {
        cleanWs()
        echo 'Initial workspace files'
        sh 'ls -ltr'
        echo 'Retrive code from GitHub repo'
        git credentialsId: 'github_access_ky', url: 'https://github.com/gdelis/cicd-pipeline-train-schedule-pipelines'
        script {
          def workspace;
          workspace = pwd()
          echo "${workspace}"
          sh 'ls -ltr'
        }
      }
      post {
        always {
          echo 'Retrieve files from remote repo:'
        }
        success {
          echo 'Successfully'
        }
        failure {
          echo 'Failed'
          echo 'Notify team from slack'
        }
      }
    }
    stage('Build') {
      steps {
        echo 'Running build stage'
        sh './gradlew build --no-daemon'
        archiveArtifacts artifacts: 'dist/trainSchedule.zip'
      }
    }
    stage('Test') {
      steps {
        echo 'Start test stage'
        // sh './gradlew test'
      }
    }
  }
  
  post {
    always {
      slackSend channel: 'pat-release-notification', color: 'green', message: 'test'
      cleanWs()
    }
  }
}
