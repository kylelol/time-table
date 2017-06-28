node('Macbuild') {
    try {
        stage('Checkout') {
          checkoutRepo()
        }
        stage('Build') {
          buildXcodeProject()
        }
        stage ('Notify') {
          notifyGithub("SUCCESS")
          notifySuccessful()
        }
      } catch(e) {
            step([
              $class: "GitHubCommitStatusSetter",
              reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/kylelol/time-table.git"],
              contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
              errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
              statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: "fail", state: "FAILURE"]]]]);
        //notifyFailed()
    }
  }

def notifyGithub(state) {
  step([
  $class: "GitHubCommitStatusSetter",
  reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/kylelol/time-table.git"],
  contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
  errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
  statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: "pass", state: state]]]]);
}

def buildXcodeProject() {
  sh 'xcodebuild -scheme "TimeTable" -configuration "Debug" build -destination "platform=iOS Simulator,name=iPhone 6,OS=10.3.1"'
}

def checkoutRepo() {
  checkout([
    $class: 'GitSCM',
    branches: [[name: 'feature/*']],
    doGenerateSubmoduleConfigurations: false,
    extensions: [], submoduleCfg: [],
    userRemoteConfigs: [[
        name: 'github',
        url: 'https://github.com/kylelol/time-table.git'
    ]]
  ])
}

def notifyStarted() {
    slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}

def notifySuccessful() {
    slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}

def notifyFailed() {
  slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}
