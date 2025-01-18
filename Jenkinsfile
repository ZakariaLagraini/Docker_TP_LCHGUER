pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'https://sonarcloud.io' // Replace with your SonarQube server URL
        SONARQUBE_TOKEN = 'baf6a8ce69de150b58c92cd1e679b3859688bfab' // Replace with your SonarQube token
    }

    stages {
        stage('SonarQube Analysis') {
            parallel {
                stage('Analyze Car Service') {
                    steps {
                        script {
                            sh 'docker-compose run --rm car-service mvn verify sonar:sonar -Dsonar.projectKey=car-service -Dsonar.host.url=$SONARQUBE_URL -Dsonar.token=$SONARQUBE_TOKEN'
                        }
                    }
                }
                stage('Analyze Client Service') {
                    steps {
                        script {
                            sh 'docker-compose run --rm client-service mvn verify sonar:sonar -Dsonar.projectKey=client-service -Dsonar.host.url=$SONARQUBE_URL -Dsonar.token=$SONARQUBE_TOKEN'
                        }
                    }
                }
                stage('Analyze Gateway Service') {
                    steps {
                        script {
                            sh 'docker-compose run --rm gateway-service mvn verify sonar:sonar -Dsonar.projectKey=gateway-service -Dsonar.host.url=$SONARQUBE_URL -Dsonar.token=$SONARQUBE_TOKEN'
                        }
                    }
                }
            }
        }

        stage('Build Services') {
            parallel {
                stage('Build MySQL') {
                    steps {
                        script {
                            sh 'docker-compose build mysql'
                        }
                    }
                }
                stage('Build Consul') {
                    steps {
                        script {
                            sh 'docker-compose build consul'
                        }
                    }
                }
                stage('Build Gateway Service') {
                    steps {
                        script {
                            sh 'docker-compose build gateway-service'
                        }
                    }
                }
                stage('Build Car Service') {
                    steps {
                        script {
                            sh 'docker-compose build car-service'
                        }
                    }
                }
                stage('Build Client Service') {
                    steps {
                        script {
                            sh 'docker-compose build client-service'
                        }
                    }
                }
                stage('Build phpMyAdmin') {
                    steps {
                        script {
                            sh 'docker-compose build phpmyadmin'
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker containers and images
            sh 'docker-compose down'
        }
    }
}