pipeline {
  agent none
  parameters {
    booleanParam(name: 'UNITTEST' , defaultValue: 'true' , description: 'Enable UnitTests ?')
    booleanParam(name: 'CODEANALYSIS' , defaultValue: 'true' , description: 'Enable Code-Anmalysis')
  }
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
        script {
          if (params.UNITTEST) {
            unitstr = ""
          }
          else {
            unitstr = "-Dmaven.test.skip=true"
          }
        echo "Building Jar Component ..."
        dir ('./samplejar') {
          sh "mvn clean package ${unitstr}"
        }
        echo "Building War Component ..."
        dir ('./samplewar') {
          sh "mvn clean package"
         }
        }
      }
    }
    stage ('Code Coverage') {
      agent {label'demo'}
      when {
        allOf {
          expression { return params.CODEANALYSIS }
          environment name: 'BUILDME' , value: 'yes'
        }
      }
      steps {
        echo "Running Code Coverage ..."
        dir ("./samplejar") {
          sh "mvn org.jacoco:jacoco-maven-plugin:0.5.5.201112152213:prepare-agent"
        }
      }
    }
    stage ('SonarQube Analysis') {
      agent { label'demo'}
       when {environment name: 'BUILDME' , value: 'yes'}
       steps {
         withSonarQubeEnv ('demosonar') {
           dir ("./samplejar") {
             sh 'mvn sonar:sonar'
           }
         }
       }
    }
    stage ('Quality Gate') {
      when {environment name: 'BUILDME' , value: 'yes'}
      steps {
        script {
          timeout (time: '10' , unit: 'MINUTES') {
            def gg = waitForQualityGate ()
            if (gg.status != 'OK') {
              error "Pipeline aborted due to quality gate failure: ${gg.status}"
            }
          }
        }
      }
    }
  }
}