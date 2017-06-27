node('Macbuild') {

    stage('Checkout/Build/Test') {

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
      		statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: "pass", state: “SUCCESS"]] ]
  ]);

        slackSend color: 'good', message: 'Message from Jenkins Pipeline'
    }
}
