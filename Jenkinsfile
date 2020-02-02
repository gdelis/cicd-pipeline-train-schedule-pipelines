def workspace;

pipeline {
  agent any
  
  stages {
    stage('Checkout GitHub Repo') {
      steps {
        echo 'Retrive code from GitHub repo'
        git credentialsId: 'github_access_ky', url: 'https://github.com/gdelis/cicd-pipeline-train-schedule-pipelines'
        workspace = pwd()
        echo "${workspace}"
        sh 'ls -ltr'
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
