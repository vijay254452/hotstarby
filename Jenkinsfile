pipeline {
    agent any

    tools {
        maven 'Maven3'           // Name of Maven configured in Jenkins
        jdk 'Java17'             // Name of JDK configured in Jenkins
        sonarScanner 'SonarScanner' // Name of SonarScanner configured in Jenkins
    }

    environment {
        SONARQUBE_SERVER = 'sonar'                 // Name of SonarQube server configured in Jenkins
        SONAR_HOST_URL = 'http://13.203.47.55:9000' // SonarQube URL
        SONAR_TOKEN = credentials('SONAR_TOKEN')   // Jenkins secret text credentials
        IMAGE_NAME = 'vijay3247/hotstar'           // Docker image name
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
                echo "Running SonarQube code analysis..."
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=hotstar-project \
                        -Dsonar.projectName=Hotstar \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.token=${SONAR_TOKEN}
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo "Waiting for SonarQube Quality Gate result..."
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Deploy Container') {
            steps {
                echo "Deploying Docker container..."
                sh "docker run -d -p 3247:8080 ${IMAGE_NAME}:latest"
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                echo "Deploying to Docker Swarm..."
                sh "docker stack deploy -c docker-compose.yml hotstar-stack"
            }
        }
    }

    post {
        success {
            echo "🏁 Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs above for errors."
        }
    }
}
