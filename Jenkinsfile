pipeline {
    agent any

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

        stage('Build and Sonar Scan') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'SONAR_ID',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
                    withSonarQubeEnv('sonarqube') {
                        sh '''
                            mvn clean package \
                                org.sonarsource.scanner.maven:sonar-maven-plugin:5.6.0.6792:sonar \
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
    }
}
