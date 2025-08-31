pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Harsha6404/hotstarby.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    # Remove old image if exists
                    docker rmi -f hotstar:v1 || true
                    # Build new image
                    docker build -t hotstar:v1 .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    # Stop and remove old container if exists
                    docker rm -f con8 || true
                    # Run new container
                    docker run -itd --name con8 -p 8008:8080 hotstar:v1
                '''
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                sh '''
                    # If service exists, update it. Otherwise, create new service
                    docker service update --image hotstar:v1 hotstarserv || \
                    docker service create --name hotstarserv -p 8009:8080 --replicas=10 hotstar:v1
                '''
            }
        }
    }
}
