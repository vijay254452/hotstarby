pipeline {
    agent any

    tools {
        maven 'Maven3'   // Name from Jenkins configuration
        jdk 'Java17'     // Ensure JDK is configured in Jenkins
    }

    environment {
        SONARQUBE_SERVER = 'sonar'  // SonarQube server name in Jenkins
        SONAR_HOST_URL = 'http://13.203.47.55:9000'
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Jenkins secret text credentials
    }

    stages {

        stage('Checkout SCM') {
            steps {
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
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t vijay3247/hotstar:latest .'
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 3247:8080 vijay3247/hotstar:latest'
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                sh 'docker stack deploy -c docker-compose.yml hotstar-stack'
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
