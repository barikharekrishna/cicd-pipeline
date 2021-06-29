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
      steps {
        script {
          env.BUILDME= "yes"
        }
      }
    }
    stage ('Build Artifacts') {
      agent {label'demo'}
      when {environment name: 'BUILDME' , value: 'yes'}
      steps {
        echo "Building Jar Component ..."
        dir ('./samplejar') {
          sh "mvn clean package"
        }
        echo "Building War Component ..."
        dir ('./samplewar') {
          sh "mvn clen package"
        }
      }
    }
  }
}