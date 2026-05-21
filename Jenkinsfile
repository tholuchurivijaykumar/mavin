pipeline {
    agent any

    environment {
        IMAGE = "vijay14082003/vijay"
        CONTAINER = "devcidoc-container"
        REPO = "https://github.com/tholuchurivijaykumar/mavin.git"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: "${REPO}"
            }
        }

        stage('Build Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Build Image') {
            steps {
                sh "ls target"
                sh "docker build -t ${IMAGE}:latest ."
                sh "docker images"
            }
        }

        stage('Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${IMAGE}:latest
                    '''
                }
            }
        }

        stage('Run') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER} || true
                    docker run -d -P --name ${CONTAINER} ${IMAGE}:latest
                '''
            }
        }
    }

    post {
        success {
            echo "SUCCESS 🚀"
        }
        failure {
            echo "FAILED ❌"
        }
    }
}
