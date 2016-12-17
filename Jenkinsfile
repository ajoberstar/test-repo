#!/usr/bin/env groovy

tokens = "${JOB_NAME}".tokenize('/')
repoOwner = tokens[tokens.size()-3]
repoName = tokens[tokens.size()-2]

if (BRANCH_NAME == 'master') {
  milestone 0
  stage('Check') {
    gradle null, 'dev', 'clean check sonarqube', false
  }

  milestone 1
  stage('Milestone Publish') {
    input message: 'Publish as milestone?'
    gradle null, 'milestone', 'clean bintrayUpload tagVersion', false
  }

  milestone 2
  stage('RC Publish') {
    input message: 'Publish as release candidate?'
    gradle null, 'rc', 'clean bintrayUpload tagVersion', false
  }

  milestone 3
  stage('Final Publish') {
    input message: 'Publish as final?'
    gradle null, 'final', 'clean gitPublishPush bintrayUpload tagVersion', false
  }
} else if (CHANGE_ID) {
  milestone 0
  stage('Check') {
    gradle null, 'dev', 'clean check sonarqube', true
  }
} else {
    stage('Unsupported') {
      echo 'Nothing\'s going to happen.'
    }
}

def gradle(scope, stage, args, preview) {
  node {
    dir(repoName) {
      checkout scm
      withCredentials([
        usernamePassword(credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GRGIT_USER', passwordVariable: 'GRGIT_PASS'),
        usernamePassword(credentialsId: 'fb3c1aa6-6b30-4f48-ba04-9fa0f489bdc5', usernameVariable: 'BINTRAY_USER', passwordVariable: 'BINTRAY_KEY')
      ]) {
        withSonarQubeEnv('SonarQube') {
          try {
            additionalArgs = ''
            if (preview) {
              additionalArgs = "-Dsonar.github.pullRequest=\"${CHANGE_ID}\" -Dsonar.github.repository=\"${repoOwner}/${repoName}\" -Dsonar.github.oauth=\"${GRGIT_PASS}\" -Dsonar.analysis.mode=preview"
            }
            sh "./gradlew --no-daemon -Psemver.stage=${stage} ${args} ${additionalArgs}"
            } finally {
              junit testResults: '**/build/test-results/**/TEST-*.xml', allowEmptyResults: true
            }
          }
        }
    }
  }
}
