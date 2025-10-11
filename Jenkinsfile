pipeline {
    agent any
    
     tools {
        jdk 'Java17'         // JDK 17 installed and named "jdk-17"
        maven 'Maven3'      // Maven 3 installed and named "maven-3"
    }
    environment {
        SONARQUBE_ENV = 'sonar' // replace with the name of your SonarQube server in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/Naveen1-6/HOTSTAR-Project.git']]
                ])
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=hotstar-project -Dsonar.projectName="HOTSTAR Project"'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 10, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build and SonarQube analysis completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs and SonarQube dashboard."
        }
    }
}
