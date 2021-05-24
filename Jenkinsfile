pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }
  environment {
    INIT = checkout scm
    NODE_ENV = 'test'
    GIT_NAME = """${sh(
      returnStdout: true,
      script: 'git --no-pager show -s --format=\'%an\' ${GIT_COMMIT}'
    ).trim()}"""
    GIT_COMMIT_SHORT = """${sh(
      returnStdout: true,
      script: 'git --no-pager show -s --format=\'%h\' ${GIT_COMMIT}'
    ).trim()}"""
    GIT_COMMIT_URL = "${env.GIT_URL[0..-5]}/commit/${env.GIT_COMMIT}"
    NODEJS_VERSION = "${readFile '.nvmrc'}".trim()
  }
  tools {
    nodejs env.NODEJS_VERSION
  }
  stages {
    stage('Setup') {
      steps {
        slackSend(color: '#D9D445', message: "STARTED: Build <${env.RUN_DISPLAY_URL}|#${env.BUILD_NUMBER}> (<${env.GIT_COMMIT_URL}|${env.GIT_COMMIT_SHORT}>) of ${env.JOB_NAME.replaceAll('%2F', '/')} by ${env.GIT_NAME}")
        sh 'node -v'
      }
    }
  }
  post {
    failure {
      slackSend(color: '#A1080D', message: "FAILED: Build <${env.RUN_DISPLAY_URL}|#${env.BUILD_NUMBER}> (<${env.GIT_COMMIT_URL}|${env.GIT_COMMIT_SHORT}>) of ${env.JOB_NAME.replaceAll('%2F', '/')} by ${env.GIT_NAME}")
    }
    success {
      slackSend(color: '#38B585', message: "SUCCESSFUL: Build <${env.RUN_DISPLAY_URL}|#${env.BUILD_NUMBER}> (<${env.GIT_COMMIT_URL}|${env.GIT_COMMIT_SHORT}>) of ${env.JOB_NAME.replaceAll('%2F', '/')} by ${env.GIT_NAME}")
    }
    always {
      sh 'rm node_modules -rf'
    }
  }
}