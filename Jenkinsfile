#!/usr/bin/env groovy

if (BRANCH_NAME == 'master') {
  milestone 0
  stage('Check') {
    gradle null, 'dev', 'clean check sonarqube'
  }

  milestone 1
  stage('Milestone Publish') {
    input message: 'Publish as milestone?'
    gradle null, 'milestone', 'clean bintrayUpload tagVersion'
  }

  milestone 2
  stage('RC Publish') {
    input message: 'Publish as release candidate?'
    gradle null, 'rc', 'clean bintrayUpload tagVersion'
  }

  milestone 3
  stage('Final Publish') {
    input message: 'Publish as final?'
    gradle null, 'final', 'clean gitPublishPush bintrayUpload tagVersion'
  }
} else if (CHANGE_ID) {
  milestone 0
  stage('Check') {
    withCredentials([usernamePassword(credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
      gradle null, 'dev', "clean check sonarqube -Dsonar.github.pullrequest=${CHANGE_ID} -Dsonar.github.oauth=${GIT_TOKEN} -Dsonar.analysis.mode=preview"
    }
  }
} else {
    stage('Unsupported') {
      echo 'Nothing\'s going to happen.'
      sh 'echo "face"'
    }
}


def gradle(scope, stage, args) {
  node {
    checkout scm
    withCredentials([
      usernamePassword(credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GRGIT_USER', passwordVariable: 'GRGIT_PASS'),
      usernamePassword(credentialsId: 'fb3c1aa6-6b30-4f48-ba04-9fa0f489bdc5', usernameVariable: 'BINTRAY_USER', passwordVariable: 'BINTRAY_KEY')
    ]) {
      withSonarQubeEnv('SonarQube') {
        try {
          sh "./gradlew --no-daemon -Psemver.stage=${stage} ${args}"
        } finally {
          junit testResults: '**/build/test-results/**/TEST-*.xml', allowEmptyResults: true
        }
      }
    }
  }
}
