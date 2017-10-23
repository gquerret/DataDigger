#!groovy

stage 'Build sonarlint-core'
node ('EC2-EU1B') {
  checkout([
    $class: 'GitSCM',
    branches: scm.branches,
    extensions: scm.extensions + [[$class: 'CleanCheckout']],
    userRemoteConfigs: scm.userRemoteConfigs
  ])
  withEnv(["PATH+ANT=${tool name: 'Ant 1.9', type: 'hudson.tasks.Ant$AntInstallation'}/bin"]) {
    bat "ant -Dsonar.host.url=http://sonar.riverside-software.fr -DDLC=Z:\\Progress\\OpenEdge-11.7 -lib Z:\\Tools\\PCT\\PCT-Latest.jar build sonar"
  }
}
