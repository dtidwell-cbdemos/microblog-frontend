library 'cb-days@tf-overhaul'
def testPodYaml = libraryResource 'podtemplates/vuejs/vuejs-test-pod.yml'
pipeline {
  agent none
  options {
    skipDefaultCheckout true
    preserveStashes(buildCount: 10)
  }
  environment {
    repoOwner = "cb-demos"
    repository = "microblog-frontend"
    gcpProject = "core-flow-research"
  }
  stages('VueJS Test & Build')
  {
    stage('VueJS Tests') {
      agent {
        kubernetes {
          label 'nodejs'
          yaml testPodYaml
        }
      }
      steps {
        checkout scm
        container('nodejs') {
          sh '''
            yarn install
            yarn test:unit
          '''
        }
      }
    }
    stage('Build and Push Image') {
      steps {
        kanikoBuildPushGeneric("${repository}", "latest", "${gcpProject}/dtidwell-cbdemos") {
          checkout scm
        }
        echo "Pushed image to gcr.io/${gcpProject}/dtidwell-cbdemos/${repository}"
      }
    }
    stage('Invoke CloudBees CD') {
      steps{
        script {
          node {
            cloudBeesFlowTriggerRelease configuration: 'Local CBCD', projectName: 'dtidwell Demo', parameters: '{"release": {"releaseName": "August Microblog Update", "stages":[{"stageName":"Release Readiness","stageValue":true}, {"stageName": "QA","stageValue":true}, {"stageName": "Pre-Prod","stageValue":true}, {"stageName": "Prod","stageValue":true}], "parameters":[{"parameterName":"microblog-frontend_version", "parameterValue": "1.0.14"}, {"parameterName": "microblog-backend_version", "parameterValue": "1.0.2"}, {"parameterName": "microblog-db_version", "parameterValue": "12.1-alpine"}]}}', releaseName: 'August Microblog Update', startingStage: 'Release Readiness'
          }
        }
      }
    }
  }
}
