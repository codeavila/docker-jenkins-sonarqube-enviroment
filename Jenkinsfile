// Function to validate that the message returned from SonarQube is OK
def qualityGateValidation(qg) {
  if (qg.status != 'OK') {
    emailext body: "WARNING: Code coverage is lower than 80% in Pipeline ${BUILD_NUMBER}", subject: 'Error Sonar Scan, Quality Gate', to: "${EMAIL_ADDRESS}"
    return true
  }
    emailext body: "CONGRATS: Code coverage is higher than 80% in Pipeline ${BUILD_NUMBER} - SUCCESS", subject: 'Info - Correct Pipeline', to: "${EMAIL_ADDRESS}"
  return false
}

pipeline {
  agent any

  tools {
    nodejs 'nodejs'
  }

  environment {
    // General Variables for Pipeline
    PROJECT_ROOT = '.'
    EMAIL_ADDRESS = 'cirexuab@gmail.com'  // Change to your real email address if notifications are configured
    REGISTRY = 'cirexuab/docker-api-example-docker-api'
  }

  stages {
    stage('Hello') {
      steps {
        // First stage is a sample hello-world step to verify correct Jenkins Pipeline
        echo 'Hello Docker-Jenkins Sonarqube'
        echo 'This is a basic environment'
      }
    }
    stage('Checkout') {
      steps {
        // Get GitHub repo using GitHub credentials (previously added to Jenkins credentials)
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/codeavila/docker-API-example']]])
      }
    }
    stage('Install dependencies') {
      steps {
        sh 'npm --version'
        sh "cd ${PROJECT_ROOT}; npm install"
      }
    }
    // stage('Unit tests') {
    //   steps {
    //     // Run unit tests
    //     sh "cd ${PROJECT_ROOT}; npm run test"
    //   }
    // }
    // stage('Generate coverage report') {
    //   steps {
    //     // Run code-coverage reports
    //     sh "cd ${PROJECT_ROOT}; npm run coverage"
    //   }
    // }
    stage('scan-sonarqube') {
      environment {
        // Previously defined in the Jenkins "Global Tool Configuration"
        scannerHome = tool 'sonar-scanner'
      }
      steps {
        // "sonarqube" is the server configured in "Configure System"
        withSonarQubeEnv('sonarqube') {
          // Execute the SonarQube scanner with desired flags
          sh "${scannerHome}/bin/sonar-scanner \
            -Dsonar.projectKey=SimpleExpressExample:Test \
            -Dsonar.projectName=SimpleExpressExample \
            -Dsonar.projectVersion=0.0.${BUILD_NUMBER} \
            -Dsonar.host.url=http://localhost:9000 \
            -Dsonar.sources=${PROJECT_ROOT}/index.js,${PROJECT_ROOT}/models/modelString.js,${PROJECT_ROOT}/routes/home.js,${PROJECT_ROOT}/routes/string.js "
            // -Dsonar.tests=${PROJECT_ROOT}/test \
            // -Dsonar.javascript.lcov.reportPaths=${PROJECT_ROOT}/coverage/lcov.info
        }
        timeout(time: 3, unit: 'MINUTES') {
          // In case of SonarQube failure or direct timeout exceed, stop Pipeline
          waitForQualityGate abortPipeline: qualityGateValidation(waitForQualityGate())
        }
      }
    }
    stage('Build docker-image') {
      steps {
        sh "cd ${PROJECT_ROOT}; docker build -t ${REGISTRY}:${BUILD_NUMBER} ."
      }
    }
    stage('Deploy docker-image') {
      steps {
        // If the Docker Hub authentication stopped, do it again
        sh 'docker login'  // This should use secure or Jenkins-managed credentials
        sh "docker push ${REGISTRY}:${BUILD_NUMBER}"
      }
    }
  }
}