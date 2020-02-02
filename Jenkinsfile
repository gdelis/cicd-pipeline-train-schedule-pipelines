pipeline {
  agent any
  
  stages {
    stage('Checkout GitHub Repo') {
      steps {
        echo 'Retrive code from GitHub repo'
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
