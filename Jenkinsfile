pipeline {
    agent any

    tools {
        maven 'maven3.9.16'
    }

    triggers {
        githubPush()
    }

    

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean compile -B -ntp'
            }
        }

        stage('Testing (JUnit + JaCoCo)') {
            steps {
                sh 'mvn test jacoco:report -B -ntp'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    recordCoverage(tools: [[parser: 'JACOCO']])
                }
            }
        }

        stage('Sonar') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    mvn sonar:sonar \
                      -Dsonar.projectKey=monolithic-app
                    '''
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests -B -ntp'
            }
        }

        stage('Upload Artifactory') {
            steps {
                sh '''
                curl -u admin:password \
                -T target/*.jar \
                $ARTIFACTORY_URL/
                '''
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
