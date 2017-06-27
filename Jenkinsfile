node('Macbuild') {
  try {
      stage('Checkout/Build/Test') {
        notifyStarted()
          // Checkout files.
          checkout([
              $class: 'GitSCM',
              branches: [[name: 'master']],
              doGenerateSubmoduleConfigurations: false,
              extensions: [], submoduleCfg: [],
              userRemoteConfigs: [[
                  name: 'github',
                  url: 'https://github.com/kylelol/time-table.git'
              ]]
          ])

          // Build and Test
          sh 'xcodebuild -scheme "TimeTable" -configuration "Debug" build test -destination "platform=iOS Simulator,name=iPhone 6,OS=10.3.1"'
      }

      stage ('Notify') {
          // Send slack notification  
  	      step([
        		$class: "GitHubCommitStatusSetter",
        		reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/kylelol/time-table.git"],
        		contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
        		errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        		statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: "pass", state: "SUCCESS"]]]]);
          notifySuccessful()
      }
    } catch(e) {
      notifyFailed
    }
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
