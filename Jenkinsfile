pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'echo "building the repo"'
      }
    }

    stage('Test') {
      steps {
        sh 'python3 test_app.py'
        script {
          try {
            input(id: "Deploy Gate", message: "Deploy ${params.project_name}?", ok: 'Deploy')
          } catch (err) {
            echo "Deployment aborted by user."
            currentBuild.result = 'ABORTED'
            error("User aborted deployment")
          }
        }
      }
    }

    stage('Deploy') {
      steps {
        echo "deploying the application"
      }
    }
  }

  post {
    always {
      echo 'The pipeline completed'
      junit allowEmptyResults: true, testResults: '**/test_reports/*.xml'
    }
    success {
      sh "nohup python3 app.py > log.txt 2>&1 &"
      echo "Flask Application Up and running!!"
    }
    failure {
      echo 'Build stage failed'
    }
  }
}
