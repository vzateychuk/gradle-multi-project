pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    stages {
        stage('Version') {
            steps {
                script {
                    sh "./gradlew -version"
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building..'
                sh "./gradlew clean build"
                archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
                sh './gradlew check'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
    post {
        always {
            junit 'library-a/build/reports/**/*'
        }
    }            
}