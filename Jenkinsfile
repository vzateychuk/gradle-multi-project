pipeline {
    agent any

    stages {
        stage('Version') {
            steps {
                script {
                    sh "echo --> begin version"
                }
                script {
                    sh "gradlew -version"
                }
                script {
                    sh "echo <-- end version"
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building..'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}