#!/usr/bin/env groovy

milestone 0
stage('Test and Analyze') {
  gradle null, 'dev', 'clean check sonarqube'
}

milestone 1
stage('Milestone') {
  input message: 'Publish as milestone?'
  gradle null, 'milestone', 'clean bintrayUpload release'
}

milestone 2
stage('RC') {
  input message: 'Publish as rc?'
  gradle null, 'rc', 'clean bintrayUpload release'
}

milestone 3
stage('Final') {
  input message: 'Publish as final?'
  gradle null, 'final', 'clean gitPublishPush bintrayUpload release'
}

def gradle(String scope, String stage, String args) {
  node {
    checkout scm
    sh 'git config --list'
    withCredentials([
      usernamePassword(credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GRGIT_USER', passwordVariable: 'GRGIT_PASS'),
      usernamePassword(credentialsId: 'fb3c1aa6-6b30-4f48-ba04-9fa0f489bdc5', usernameVariable: 'BINTRAY_USER', passwordVariable: 'BINTRAY_KEY')
    ]) {
      withSonarQubeEnv('SonarQube') {
        try {
          sh "./gradlew --no-daemon -Psemver.stage=${stage} ${args}"
        } finally {
          junit '**/build/test-results/**/TEST-*.xml'
        }
      }
    }
  }
}
