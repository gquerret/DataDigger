pipeline {
  agent { label 'windows' }
  options {
    buildDiscarder(logRotator(numToKeepStr:'5'))
    timeout(time: 30, unit: 'MINUTES')
    skipDefaultCheckout
  }
  stages {
    stage ('Build') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: scm.branches,
          extensions: scm.extensions + [[$class: 'CleanCheckout']],
          userRemoteConfigs: scm.userRemoteConfigs
        ])
        withEnv(["PATH+ANT=${tool name: 'Ant 1.9', type: 'hudson.tasks.Ant$AntInstallation'}/bin",
                 "DLC=${tool name: 'OpenEdge-11.7', type: 'jenkinsci.plugin.openedge.OpenEdgeInstallation'}"]) {
          bat "ant -DDLC=%DLC% -lib PCT.jar build dist"
        }
        stash name: 'windows-build', includes: 'target/DataDigger.zip'
        archiveArtifacts artifacts: 'target/DataDigger.zip'
      }
    }

    stage ('Code analysis') {
      when {
        branch "*/develop"
      }
      steps {
        withEnv(["PATH+ANT=${tool name: 'Ant 1.9', type: 'hudson.tasks.Ant$AntInstallation'}/bin", "DLC=${tool name: 'OpenEdge-11.7', type: 'jenkinsci.plugin.openedge.OpenEdgeInstallation'}"]) {
          bat "ant -Dsonar.host.url=http://sonar.riverside-software.fr -DDLC=%DLC% -lib PCT.jar sonar"
        }
      }
    }

    stage ('Test install') {
      agent {
        node {
          label 'windows'
          customWorkspace "Z:\\TestDeployment\\DD-${BRANCH_NAME}"
        }
      }
      steps {
        unstash name: 'windows-build'
        unzip zipFile: 'target/DataDigger.zip'
      }
    }

  }
}
