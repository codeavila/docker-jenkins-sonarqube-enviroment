pipeline {
  agent any

  tools {
    nodejs 'nodejs'
  }

  environment {
    // General Variables for Pipeline
    PROJECT_ROOT = '.'
    EMAIL_ADDRESS = 'correo@gmail.com'  // Cambia esto por tu dirección de correo real si las notificaciones están configuradas
    REGISTRY = 'usuario/nombre-imagen-dockerhub' // Cambiar por tu usuario de dockerhub y tu nombre de imagen previamente registrada
  }

  stages {
    stage('Hello') {
      steps {
        // Primer paso es un saludo para verificar el correcto funcionamiento del pipeline
        echo 'Hello Docker-Jenkins Sonarqube'
        echo 'This is a basic environment'
      }
    }
    stage('Checkout') {
      steps {
        // Obtener el repositorio de GitHub usando las credenciales de GitHub (previamente añadidas a Jenkins)
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/codeavila/docker-API-example']]])
      }
    }
    stage('Install dependencies') {
      steps {
        sh 'npm --version'
        sh "cd ${PROJECT_ROOT}; npm install"
      }
    }
    stage('scan-sonarqube') {
      environment {
        // Previamente definido en la "Configuración de Herramientas Globales" de Jenkins
        scannerHome = tool 'sonar-scanner'
      }
      steps {
        // "sonarqube" es el servidor configurado en "Configure System"
        withSonarQubeEnv('sonarqube') {
          // Ejecutar el escáner SonarQube con los flags deseados
          sh "${scannerHome}/bin/sonar-scanner \
            -Dsonar.projectKey=SimpleExpressExample:Test \
            -Dsonar.projectName=SimpleExpressExample \
            -Dsonar.projectVersion=0.0.${BUILD_NUMBER} \
            -Dsonar.host.url=http://mysonarqube:9000 \
            -Dsonar.sources=${PROJECT_ROOT}/index.js,${PROJECT_ROOT}/models/modelString.js,${PROJECT_ROOT}/routes/home.js,${PROJECT_ROOT}/routes/string.js "
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
        // Si la autenticación de Docker Hub se detuvo, hazlo de nuevo
        sh 'docker login'  // Debería usar credenciales seguras o gestionadas por Jenkins
        sh "docker push ${REGISTRY}:${BUILD_NUMBER}"
      }
    }
  }
}
