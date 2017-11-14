pipeline {
  agent { label 'windows' }
  options {
    buildDiscarder(logRotator(numToKeepStr:'5'))
    timeout(time: 30, unit: 'MINUTES')
  }
  parameters {
    string(name: 'TARGET', 
           description: "Target environment",
           defaultValue: 'staging')
  }    
  stages {
    stage ('Build') {
      steps {
        withEnv(["PATH+ANT=${tool name: 'Ant 1.9', type: 'hudson.tasks.Ant$AntInstallation'}/bin",
                 "DLC=${tool name: 'OpenEdge-11.7', type: 'jenkinsci.plugin.openedge.OpenEdgeInstallation'}"]) {
          bat "ant -DDLC=%DLC% -lib PCT.jar build dist"
          bat "echo $TARGET"
        }
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
  }
}
