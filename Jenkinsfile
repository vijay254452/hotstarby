pipeline {
    agent any

    tools {
        maven 'Maven3'      // Maven configured in Jenkins
        jdk 'Java17'        // JDK configured in Jenkins
    }

    environment {
        SONARQUBE_SERVER = 'sonar'                   // SonarQube server name in Jenkins
        SONAR_HOST_URL = 'http://13.203.47.55:9000' // SonarQube URL
        SONAR_TOKEN = credentials('SONAR_TOKEN')     // Jenkins secret text
        IMAGE_NAME = 'vijay3247/hotstar'            // Docker image name
    }

    stages {

        stage('Checkout SCM') {
            steps {
                echo "Checking out code from GitHub..."
                git branch: 'main', url: 'https://github.com/vijay254452/hotstarby.git'
            }
        }

        stage('Build WAR') {
            steps {
                echo "Building project with Maven..."
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=hotstar-project \
                        -Dsonar.projectName=Hotstar \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sourc
