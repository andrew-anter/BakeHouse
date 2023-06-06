
pipeline {
    agent {
    node {
        label 'jenkins-ubuntu-slave'
        }
    }
    parameters {
        choice(name: 'ENV', choices: ['dev', 'test', 'prod',"release"])
    } 
    stages {
        stage('build') {
            steps {
                echo 'building...'
                script{
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        docker login -u ${USERNAME} -p ${PASSWORD}
                        docker build . -t andrewanter/bakehouse:v1
                        docker push andrewanter/bakehouse:v1
                    }
                }
                
            }
        }

        stage('test'){
            steps{
                echo 'test'
            }
        }

        stage('deploy') {
            steps {
                echo 'deploy'
            }
        }
    }
}
