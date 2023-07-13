pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    stages {
        stages {

        stage('Buld Info') {
            steps {
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "Build #${env.BUILD_NUMBER}, tag ${env.BUILD_TAG}"
                echo "Build URL ${env.BUILD_URL}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Job name: ${env.JOB_NAME}"
            }
        }

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