#!/usr/bin/env groovy
pipeline {
  agent { label 'jdk8' }
  parameters {
    string(name: 'SCOPE', defaultValue: '', description: 'Change Scope?')
    string(name: 'STAGE', defaultValue: '', description: 'Change Stage?')
  }
  environment {
    GRGIT_USER = credentials('github-build-token')
  }
  stages {
    stage('Check') {
      steps {
        sh "./gradlew clean check '-Preckon.scope=${params.SCOPE}' '-Preckon.stage=${params.STAGE}'"
      }
      post {
        always {
          junit testResults: '**/build/test-results/**/TEST-*.xml', allowEmptyResults: true
        }
      }
    }
    stage('Publish') {
      when { branch 'master' }
      steps {
        sh './gradlew publish'
      }
    }
    stage('Release?') {
      when { branch 'master' }
      steps {
        input message: 'Release this version?'
      }
    }
    stage('Release') {
      when { branch 'master' }
      steps {
        sh "./gradlew tagVersion '-Preckon.scope=${params.SCOPE}' '-Preckon.stage=${params.STAGE}'"
      }
    }
  }
}
