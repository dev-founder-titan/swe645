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
                    def customImage = docker.build("devbravo1996/surveyform:${BUILD_TIMESTAMP}")
                }
            }
        }
        stage("Pushing Image to DockerHub") {
            steps {
                script {
                    sh 'docker push devbravo1996/surveyform:${BUILD_TIMESTAMP}'
                }
            }
        }
        stage("Deploying to Rancher") {
            steps {
                sh 'kubectl set image deployment/surveyform surveyform=devbravo1996:${BUILD_TIMESTAMP} -n surveyform'
            }
        }
    }
}