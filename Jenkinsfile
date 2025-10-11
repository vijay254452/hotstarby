pipeline {
    agent any

    environment {
        // SonarQube token stored in Jenkins Credentials
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        DOCKER_IMAGE = "vijay3247/myapp:latest"
    }

    tools {
        maven 'Maven3' // Name of Maven configured in Jenkins Global Tool Config
        jdk 'Java17'   // Name of JDK configured in Jenkins Global Tool Config
        // Note: SonarScanner should also be configured in Jenkins Tools if needed
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
                withSonarQubeEnv('sonar') {
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=hotstar-project \
                        -Dsonar.projectName=Hotstar \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes \
                        -Dsonar.host.url=http://13.203.47.55:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo "Checking SonarQube Quality Gate..."
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Deploy Container') {
            steps {
                echo "Deploying Docker container..."
                sh """
                    docker stop myapp || true
                    docker rm myapp || true
                    docker run -d --name myapp -p 3247:8080 $DOCKER_IMAGE
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs above for errors."
        }
    }
}
