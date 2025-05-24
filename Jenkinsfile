pipeline {
  agent any

  environment {
    SCHEME = "SampleXCUITests"
    DESTINATION = "platform=iOS Simulator,name=iPhone 16 Plus,OS=18.4"
    WORKSPACE = "Sample iOS.xcworkspace"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[
            url: 'https://github.com/sikandarim/XCUItest.git'
          ]]
        ])
      }
    }

    stage('Install Pods') {
      steps {
        sh '''
          export PATH="$HOME/.rbenv/shims:$PATH"
          pod install
        '''
      }
    }

    stage('Build & Test (Simulator)') {
      steps {
        sh '''
          export PATH="$HOME/.rbenv/shims:$PATH"
          mkdir -p build
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

