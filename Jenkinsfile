pipeline {
    agent any

    environment {
        SONAR_TOKEN = 'baf6a8ce69de150b58c92cd1e679b3859688bfab'
    }

    stages {
        stage('Git Clone') {
            steps {
                script {
                    checkout([$class: 'GitSCM', branches: [[name: 'master']],
                        userRemoteConfigs: [[url: 'https://github.com/ZakariaLagraini/Docker_TP_LCHGUER.git']]])
                }
            }
        }

         stage('Build Client Service') {
            steps {
                dir('client') {
                    bat 'mvn clean install'
                }
            }
        }
        
        stage('Build Car Service') {
            steps {
                dir('car') {
                    bat 'mvn clean install'
                }
            }
        }
        
        stage('Build Gateway Service') {
            steps {
                dir('gateway') {
                    bat 'mvn clean install'
                }
            }
        }
        stage('SonarQube Analysis') {
            parallel {
                stage('SonarQube Analysis - Client') {
                    steps {
                        dir('client') {
                            bat """
                                mvn clean verify sonar:sonar ^
                                -Dsonar.projectKey=consul-micro_client ^
                                -Dsonar.organization=consul-micro ^
                                -Dsonar.host.url=https://sonarcloud.io ^
                                -Dsonar.token=${SONAR_TOKEN}
                            """
                        }
                    }
                }
                
                stage('SonarQube Analysis - Car') {
                    steps {
                        dir('car') {
                            bat """
                                mvn clean verify sonar:sonar ^
                                -Dsonar.projectKey=consul-micro_car ^
                                -Dsonar.organization=consul-micro ^
                                -Dsonar.host.url=https://sonarcloud.io ^
                                -Dsonar.token=${SONAR_TOKEN}
                            """
                        }
                    }
                }
                
                stage('SonarQube Analysis - Gateway') {
                    steps {
                        dir('gateway') {
                            bat """
                                mvn clean verify sonar:sonar ^
                                -Dsonar.projectKey=consul-micro_gateway ^
                                -Dsonar.organization=consul-micro ^
                                -Dsonar.host.url=https://sonarcloud.io ^
                                -Dsonar.token=${SONAR_TOKEN}
                            """
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