node {
  stage 'Checkout'
  checkout scm

  stage 'Publish Site'
  withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: '29490691-342d-4fa1-b0dc-1e3e27e8e0fa', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS']]) {
    sh './gradlew -Dorg.ajoberstar.grgit.auth.username=$GIT_USER -Dorg.ajoberstar.grgit.auth.password=$GIT_PASS gitPublishPush --stacktrace'
  }
}
