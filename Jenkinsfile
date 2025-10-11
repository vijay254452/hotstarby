pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'sonar'             // Jenkins SonarQube server name (configured in Manage Jenkins → System)
        SCANNER_HOME = tool 'SonarScanner'  // SonarQube Scanner tool name (configured in Manage Jenkins → Tools)
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code from GitHub..."
                git branch: 'main', url: 'https://github.com/vijay254452/hotstarby.git'
                sh 'pwd && ls -l'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Running SonarQube code analysis..."
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh '''
                        ${SCANNER_HOME}/bin/sonar-scanner \
                          -Dsonar.projectKey=hotstar-project \
                          -Dsonar.projectName=Hotstar \
                          -Dsonar.projectVersion=1.0 \
                          -Dsonar.sources=src \
                          -Dsonar.java.binaries=target \
                          -Dsonar.host.url=http://http://13.203.47.55/:9000 \
                          -Dsonar.login=sonar
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo "Waiting for SonarQube quality gate result..."
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build WAR') {
            steps {
                echo "Building Maven WAR package..."
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh '''
                    docker rmi -f hotstar:v1 || true
                    docker build -t hotstar:v1 -f /var/lib/jenkins/workspace/hotsatr/Dockerfile /var/lib/jenkins/workspace/hotsatr
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo "Deploying container locally..."
                sh '''
                    docker rm -f con8 || true
                    docker run -d --name con8 -p 8008:8080 hotstar:v1
                '''
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                echo "Deploying on Docker Swarm..."
                sh '''
                    docker service update --image hotstar:v1 hotstarserv || \
                    docker service create --name hotstarserv -p 8009:8080 --replicas=10 hotstar:v1
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs above for errors."
        }
        always {
            echo "🏁 Pipeline completed!"
        }
    }
}
