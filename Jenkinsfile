#!groovy

pipeline {
  agent { label 'windows' }
  options {
    buildDiscarder(logRotator(numToKeepStr:'5'))
    timeout(time: 30, unit: 'MINUTES')
  }
  stages {
    stage ('Build') {
      checkout([
        $class: 'GitSCM',
        branches: scm.branches,
        extensions: scm.extensions + [[$class: 'CleanCheckout']],
        userRemoteConfigs: scm.userRemoteConfigs
      ])
      withEnv(["PATH+ANT=${tool name: 'Ant 1.9', type: 'hudson.tasks.Ant$AntInstallation'}/bin", "DLC=${tool name: 'OpenEdge-11.7', type: 'jenkinsci.plugin.openedge.OpenEdgeInstallation'}"]) {
        bat "ant -Dsonar.host.url=http://sonar.riverside-software.fr -DDLC=%DLC% -lib PCT.jar build dist sonar"
      }
      archiveArtifacts artifacts: 'target/DataDigger.zip'
    }
  }
}
