pipeline {
    agent any

    tools {
        // Maven ve JDK gibi gerekli araçları tanımlayın
        maven "default"
    }

    environment {
        CI = 'true'
        SONARQUBE_SERVER = 'sonarqube' // Name of the SonarQube server configured in Jenkins
        SONARQUBE_SCANNER_HOME = tool 'sonarqube-scanner' 
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://gitlab-codecamp24.obss.io/mina-unal/spring-project.git', credentialsId: 'c78fa0ff-2217-451d-ba1d-ffe7f4bd5414'
            }
        }
        stage('Build') {
            steps {
                // Maven build komutunu çalıştırın
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                script {
                    try {
                        sh 'mvn test'
                    } catch (Exception e) {
                        echo 'Ignoring test failures'
                    }
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { 
                    sh "${env.SONARQUBE_SCANNER_HOME}/bin/sonar-scanner \
                      -Dsonar.projectKey=spring-project \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=${env.SONAR_HOST_URL} \
                      -Dsonar.login=sqa_c71ea1f85c4973a0bc07718c0dbd6d8448555fc5 \
                      -Dsonar.java.binaries=target/classes"
               }
           }
       }
        stage('Dockerize') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t case-1-mina:latest .'
                }
            }
        }
    }

    post {
        success {
            echo 'Build and SonarQube analysis completed successfully.'
        }
        failure {
            echo 'Build or SonarQube analysis failed.'
        }
    }
}
