pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token') // Replace with your Jenkins SonarQube token ID
    }

    tools {
        maven 'Maven3'            // Name of Maven installation in Jenkins
        // Note: SonarScanner will be invoked via 'tool' in the steps
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
                script {
                    echo "Running SonarQube analysis..."
                    withSonarQubeEnv('sonar') {  // Name of your SonarQube server in Jenkins
                        def scannerHome = tool 'SonarScanner'  // Name of SonarScanner in Jenkins Tools
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
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
                echo "Building Docker image..."
                sh "docker build -t vijay3247/restaurant-site:latest ."
            }
        }

        stage('Deploy Container') {
            steps {
                echo "Deploying Docker container..."
                sh "docker run -d -p 3247:8080 vijay3247/restaurant-site:latest"
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                echo "Deploying on Docker Swarm..."
                sh "docker stack deploy -c docker-compose.yml myapp-stack"
            }
        }
    }

    pos
