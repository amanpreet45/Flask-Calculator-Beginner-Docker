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
            def projectName = params.project_name ?: "Unknown Project"
            input(id: "Deploy Gate", message: "Deploy ${projectName}?", ok: 'Deploy')
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
        echo "Deploying the application"
      }
    }
  }

  post {
    always {
      echo 'The pipeline completed'
      junit allowEmptyResults: true, testResults: '**/test_reports/*.xml'
    }
    success {
      script {
        echo "Starting Flask Application..."
        sh "nohup python3 app.py > log.txt 2>&1 & echo \$! > app.pid"
        echo "Flask Application Up and running!!"
      }
    }
    failure {
      echo 'Build stage failed'
    }
  }
}
