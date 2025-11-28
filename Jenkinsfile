pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2'
    }

    environment {
        DOCKER_IMAGE = "saifelislem/student-management:1.0"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/saifelislem/devops.git'
            }
        }

        stage('Build Maven') {
            steps {
                echo 'Building Maven project...'
                dir('student-management') {
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                dir('student-management') {
                    sh "docker build -t $DOCKER_IMAGE ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Logging into Docker and pushing image...'
                dir('student-management') {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-token-str',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push $DOCKER_IMAGE"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build et push Docker terminés avec succès !'
            archiveArtifacts artifacts: 'student-management/target/*.jar', fingerprint: true
        }
        failure {
            echo 'pipeline échoué.'
        }
        always {
            echo 'Pipeline terminé.'
        }
    }
}
