pipeline {
  agent none
  stages {
    stage ('Checkout') {
      agent { label'demo' }
      steps {
        git branch: 'master' , url: 'https://gitlab.com/debasis4/cicdpipeline.git'
      }
    }
    stage ('PreCheck') {
      agent { label'demo' }
      when {
        anyOf {
            changeset "samplejar/**"
            changeset "samplewar/**"
        }
      }
      steps {
        script {
          env.BUILDME= "yes"
        }
      }
    }
  }
}