pipeline {
    agent any

    environment {
        APP_EC2_IP = '15.207.71.7'
    }

    tools {
        jdk 'JAVA'
        maven 'MAVEN'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git url: 'https://github.com/NithinAnde-SalohiTech/spring-petclinic-nithin.git',
                    branch: 'main'
            }
        }

        stage('Validate') {
            steps {
                sh 'mvn validate'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Sonar Scan') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'SONAR_ID',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
                    withSonarQubeEnv('sonarqube') {
                        sh '''
                            mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.6.0.6792:sonar
                                -Dsonar.projectKey=nithinande-salohitech \
                                -Dsonar.organization=nithinande-salohitech \
                                -Dsonar.host.url=https://sonarcloud.io \
                                -Dsonar.token=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                        -t nithinandedocker/spring-petclinic:latest .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'DOCKER_ID',
                        variable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "nithinandedocker" \
                            --password-stdin

                        docker push nithinandedocker/spring-petclinic:latest
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'APP_EC2_SSH',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            -i "$SSH_KEY" \
                            "$SSH_USER@$APP_EC2_IP" \
                            "
                                docker pull nithinandedocker/spring-petclinic:latest &&
                                docker stop spring-petclinic || true &&
                                docker rm spring-petclinic || true &&
                                docker run -d \
                                    --name spring-petclinic \
                                    -p 8080:8080 \
                                    nithinandedocker/spring-petclinic:latest
                            "
                    '''
                }
            }
        }
    }
}
