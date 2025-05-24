pipeline {
  agent any

  environment {
    SCHEME = "SampleXCUITests"
    DESTINATION = "platform=iOS Simulator,name=iPhone 16 Plus,OS=18.4"
    WORKSPACE = "SampleApp.xcworkspace" // or use .xcodeproj if no workspace
  }

  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']], // Change to your branch if different
          userRemoteConfigs: [[
            url: 'https://github.com/sikandarim/XCUItest.git',
            credentialsId: 'github-creds' // The ID you set in Jenkins credentials
          ]]
        ])
      }
    }

    stage('Install Pods') {
      when {
        expression { fileExists('Podfile') }
      }
      steps {
        sh 'pod install'
      }
    }

    stage('Build & Test (Simulator)') {
      steps {
        sh '''
          xcodebuild \
            -workspace "$WORKSPACE" \
            -scheme "$SCHEME" \
            -destination "$DESTINATION" \
            clean test \
            -derivedDataPath build/DerivedData \
            | tee build/xcuitest.log | xcpretty --report junit
        '''
      }
    }

    stage('Publish Results') {
      steps {
        junit 'build/reports/junit.xml'
        archiveArtifacts artifacts: 'build/xcuitest.log', fingerprint: true
      }
    }
  }
}

