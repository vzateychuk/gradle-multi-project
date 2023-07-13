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
            when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
                echo 'Deploying....'
            }
        }
    }

    post {
        always {
            echo 'Post Action (collect test reports)...'
            // junit 'library-a/build/reports/**/*'
        }
    }            
}