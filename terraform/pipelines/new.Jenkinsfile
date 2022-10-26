pipeline {
  agent {
      docker {
          args '-e "HOME=/home/vagrant/jenkins/workspace/testjobs/dockerexample"'                        
          image 'python:slim' }
       }
  stages {
         stage('Get Code') {
            steps {
                 git 'https://github.com/agridyaev/otus-allure/'
            }
         }
    stage('build') {
      steps {
        sh 'pip install --user -r requirements.txt'
      }
    }
    stage('test') {
      steps {
        sh 'python -m pytest --junitxml=./test-reports/report.xml ./tests'
      }
      post {
        always {
          junit 'test-reports/*.xml'
        }
      }
    }
  }
}