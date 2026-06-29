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
        stage('Compile') {
            steps {
                sh 'mvn clean compile -B -ntp'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test -B -ntp' 
            }
            post { 
                success {
                    junit 'target/surefire-reports/*.xml' 
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
        stage('Package') {A
            steps {
                sh 'mvn package -DskipTests -B -ntp'
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
