pipeline{
    agent any
    tools{
        jdk 'JAVA'
        maven 'MAVEN'
    }

    stages{
        stage('git checkout'){
            steps{
                git url: 'https://github.com/NithinAnde-SalohiTech/spring-petclinic-nithin.git',
                    branch:'main'
            }
        }
        stage('validate'){
            steps{
                sh 'mvn validate'
            }
        }
        stage('Test'){
            steps{
                sh 'mvn test'
            }
        }
        stage('compile'){
            steps{
                sh 'mvn compile'
            }
        }
        stage("build,scan and run"){
            steps{
                withCredentials([string(credentialsId: 'SONAR_ID', variable: 'SONAR_TOKEN')]){
                    withSonarQubeEnv('sonarqube'){
                        sh '''mvn clean package org.sonarsource.scanner.maven:sonar-maven-plugin:5.6.0.6792:sonar \
                        -Dsonar.projectKey=nithinande-salohitech \
                        -Dsonar.organization=NithinAnde-SalohiTech \
                        -Dsonar.host.url=https://sonarcloud.io \
                        -Dsonar.login=$SONAR_TOKEN '''
                    }
                }
            }
        }
    }
}
