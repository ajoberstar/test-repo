#!/usr/bin/env groovy

milestone 0
stage('Test and Analyze') {
  echo "$JOB_NAME"
  echo "$JOB_BASE_NAME"
  gradle null, 'dev', 'check sonarqube'
}

milestone 1
stage('Milestone') {
  input message: 'Publish as milestone?'
  gradle null, 'milestone', 'bintrayUpload release'
}

milestone 2
stage('RC') {
  input message: 'Publish as rc?'
  gradle null, 'rc', 'bintrayUpload release'
}

milestone 3
stage('Final') {
  input message: 'Publish as final?'
  gradle null, 'final', 'gitPublishPush bintrayUpload release'
}

def gradle(String scope, String stage, String args) {
  node {
    checkout scm
    withCredentials([
      usernamePassword(credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GRGIT_USER', passwordVariable: 'GRGIT_PASS'),
      usernamePassword(credentialsId: 'fb3c1aa6-6b30-4f48-ba04-9fa0f489bdc5', usernameVariable: 'BINTRAY_USER', passwordVariable: 'BINTRAY_KEY')
    ]) {
      withSonarQubeEnv('SonarQube') {
        try {
          sh "./gradlew -Psemver.stage=${stage} ${args}"
        } finally {
          junit '**/build/test-results/**/TEST-*.xml'
        }
      }
    }
  }
}
