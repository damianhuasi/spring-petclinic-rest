pipeline {
    agent any
    tools {
        maven 'maven3.9.16'
    }
    triggers {
        // cron('* * * * *')
        githubPush()
    }
    stages {
        // stage('Checkout SCM') {
        //     steps {
        //         git branch: 'master', url: 'https://github.com/devops-instructor/spring-petclinic-rest.git'
        //     }
        // }
        stage('Compile') {
            steps {
                sh 'mvn clean compile -B -ntp'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test -B -ntp'
                // sh 'mvn test -Dmaven.test.failure.ignore=true -B -ntp'
            }
            post { 
                success {
                    junit 'target/surefire-reports/*.xml'
                    // junit skipMarkingBuildUnstable: true, testResults: 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Coverage') {
            steps {
                sh 'mvn jacoco:report -B -ntp'
            }
            post { 
                success {
                    recordCoverage(tools: [[parser: 'JACOCO']])
                }
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package -DskipTests -B -ntp'
            }
        }
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'env | sort'
                    sh 'mvn sonar:sonar -B -ntp'
                }
            }
        }
    }
    post { 
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
        cleanup {
            cleanWs()
        }
    }
}
