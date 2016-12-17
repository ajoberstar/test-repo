node {
  stage 'Checkout'
  checkout scm

  stage 'Publish Site'
  sh './gradlew gitPublishPush --stacktrace'
}
