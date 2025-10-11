pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Jenkins credential ID
        IMAGE_NAME = 'vijay3247/restaurant-site'
    }

    tools {
        maven 'Maven3' // Name of Maven configured in Jenkins
        dockerTool 'Docker' // Optional if using Docker plugin
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
                echo "Running SonarQube analysis in Docker..."
                sh """
                docker run --rm \
                    -v ${WORKSPACE}:/usr/src \
                    -w /usr/src \
                    sonarsource/sonar-scanner-cli:latest \
                    -Dsonar.projectKey=hotstar-project \
                    -Dsonar.projectName=Hotstar \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.host.url=http://13.203.47.55:9000 \
                    -Dsonar.login=${SONAR_TOKEN}
                """
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
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Deploy Container') {
            steps {
                sh "docker run -d --name myapp -p 3247:8080 ${IMAGE_NAME}:latest"
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                sh 'docker stack deploy -c docker-compose.yml myapp-stack'
            }
        }

    }

    post {
        always {
