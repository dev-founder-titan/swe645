pipeline{
    agent any
    environment {
        DOCKERHUB_PASS = credentials('docker-pass')
    }

    stages {
        stage("Building Student Survey Form page") {
            steps {
                script {
                    checkout scm
                    sh "rm -rf *.war"
                    sh 'jar -cvf SurveyForm.war *'
                    sh 'echo ${BUILD_TIMESTAMP}'
                    sh 'docker login -u devbravo1996'
                }
            }
        }
    }
}