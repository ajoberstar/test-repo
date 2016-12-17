#!/usr/bin/env groovy
node {
  stage 'Checkout'
  checkout scm

  stage 'Publish Site'
  withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GRGIT_USER', passwordVariable: 'GRGIT_PASS']]) {
    sh './gradlew gitPublishPush'
  }
}
