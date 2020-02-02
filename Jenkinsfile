pipeline {
  agent any
  
  stages {
    stage('Checkout GitHub Repo') {
      steps {
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
    }
    stage('Build') {
      steps {
        echo 'Running build stage'
        sh './gradlew build --no-daemon'
        archiveArtifacts artifacts: 'dist/trainSchedule.zip'
      }
    }
  }
  
  post {
    always {
      cleanWs()
    }
  }
}
