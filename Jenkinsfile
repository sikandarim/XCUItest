pipeline {
  agent any

  environment {
    SCHEME = "SampleXCUITests"
    WORKSPACE = "Sample iOS.xcodeproj"
    DESTINATION = "platform=iOS Simulator,name=iPhone 16 Plus"
    SIMULATOR_UDID = "32F9086D-FE05-4B09-A168-34F7AEBFD67D"
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

          echo "🔄 Booting simulator if not already booted..."
          xcrun simctl boot "$SIMULATOR_UDID" || true

          echo "🛠️ Running tests on: $DESTINATION"
          mkdir -p build
          xcodebuild \
            -project "$WORKSPACE" \
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

