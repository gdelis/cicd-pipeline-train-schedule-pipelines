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
      steps {
        echo 'Deploy to staging'
        withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
          sshPublisher(
            failOnError: true,
            continueOnError: false,
            publishers: [
              sshPublisherDesc(
                configName: 'staging',
                sshCredentials: [
                  username: "$USERNAME",
                  encryptedPassphrase: "$USERPASS"
                ], 
                transfers: [
                  sshTransfer(
                    sourceFiles: 'dist/trainSchedule.zip',
                    removePrefix: 'dist/',
                    remoteDirectory: '/tmp',
                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                  )
                ]
              )
            ]     
          )
        }
      }
    }
    stage('DeployToProduction') {
      steps {
        echo "Branch: ${branch}"
        
        input 'Does the staging environment look OK?'
        echo 'Deploy to production'
        withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
          sshPublisher(
            failOnError: true,
            continueOnError: false,
            publishers: [
              sshPublisherDesc(
                configName: 'production',
                sshCredentials: [
                  username: "$USERNAME",
                  encryptedPassphrase: "$USERPASS"
                ], 
                transfers: [
                  sshTransfer(
                    sourceFiles: 'dist/trainSchedule.zip',
                    removePrefix: 'dist/',
                    remoteDirectory: '/tmp',
                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                  )
                ]
              )
            ]     
          )
        }
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
