pipeline {
  agent any
  
  parameters {
    booleanParam(name: 'executeTestStage', defaultValue: false, description: 'Execute testing stage?')
  }
  
  stages {
    stage('Checkout GitHub Repo') {
      steps {
        cleanWs()
        git credentialsId: 'github_access_key', url: 'https://github.com/gdelis/cicd-pipeline-train-schedule-pipelines'
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
      when {
        expression {
          executeTestStage
        }
      }
      steps {
        echo 'Executing (in parallel) test stage'
      }
    }
    stage('DeployToStaging') {
      when {
        branch 'master'
      }
      steps {
        echo 'Deploy to staging'
      }
    }
    stage('DeployToProduction') {
      when {
        branch 'master'
      }
      steps {
        input 'Does the staging environment look OK?'
        echo 'Deploy to production'
      }
    }
  }
  
  post {
    always {
      // slackSend channel: 'pat-release-notification', color: 'green', message: 'test'
      cleanWs()
    }
  }
}
