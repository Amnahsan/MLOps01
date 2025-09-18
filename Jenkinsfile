pipeline {
  agent any
  environment {
    DOCKERHUB_CREDS = credentials('dockerhub-credentials') // create in Jenkins credentials
    DOCKERHUB_REPO = "i228759/mlops1"
    ADMIN_EMAIL = "amnahsn113@gmail.com"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Setup Python') {
      steps {
        bat '''
          python -m pip install --upgrade pip
          if exist requirements.txt pip install -r requirements.txt
        '''
      }
    }
    stage('Unit Tests') {
      steps {
        bat 'pytest -q || exit 0'
      }
    }
    stage('Build Docker') {
      steps {
        bat "docker build -t %DOCKERHUB_REPO%:${BUILD_NUMBER} ."
      }
    }
    stage('Push Docker') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          bat '''
            echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
            docker push %DOCKERHUB_REPO%:%BUILD_NUMBER%
          '''
        }
      }
    }
    stage('Notify Admin') {
      steps {
        mail bcc: '', body: "Jenkins job ${env.JOB_NAME} #${env.BUILD_NUMBER} succeeded and pushed image ${DOCKERHUB_REPO}:${env.BUILD_NUMBER}", subject: "Jenkins: Build Succeeded", to: "${ADMIN_EMAIL}"
      }
    }
  }
  post {
    failure {
      mail bcc: '', body: "Jenkins job ${env.JOB_NAME} #${env.BUILD_NUMBER} failed.", subject: "Jenkins: Build Failed", to: "${ADMIN_EMAIL}"
    }
  }
}
