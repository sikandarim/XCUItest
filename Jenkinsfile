pipeline {
  agent any

  environment {
    SCHEME = "SampleXCUITests"
    WORKSPACE = "Sample iOS.xcodeproj"
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

    stage('Build & Test (Simulator)') {
      steps {
        sh '''
          export LANG=en_US.UTF-8
          export LC_ALL=en_US.UTF-8
          export PATH="/usr/local/bin:$PATH"
          mkdir -p build
          xcodebuild \
            -project "$WORKSPACE" \
            -scheme "$SCHEME" \
            -destination id=32F9086D-FE05-4B09-A168-34F7AEBFD67D \
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

