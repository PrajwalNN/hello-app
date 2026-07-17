pipeline {

    agent any

    environment {

        IMAGE_NAME="prajwalnn/hello-app"

    }

    triggers {

        cron('H/5 * * * *')

    }

    stages {

        stage('Checkout') {

            steps {

                git 'https://github.com/PrajwalNN/hello-app.git'

            }

        }

        stage('Build') {

            steps {

                sh 'mvn clean package'

            }

        }

        stage('Test') {

            steps {

                sh 'mvn test'

            }

        }

        stage('Docker Build') {

            steps {

                sh 'docker build -t $IMAGE_NAME:latest .'

            }

        }

        stage('Docker Push') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }

            }

        }

        stage('Deploy to Docker Swarm') {

            steps {

                sh '''
                docker service update \
                --image $IMAGE_NAME:latest hello-service \
                || \
                docker service create \
                --name hello-service \
                -p 8080:8080 \
                $IMAGE_NAME:latest
                '''
            }

        }

    }

}
