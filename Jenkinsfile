
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
                    sh '''    
                        docker login -u ${USERNAME} -p ${PASSWORD}
                        docker build . -t andrewanter/bakehouse:${BUILD_NUMBER}
                        docker push andrewanter/bakehouse:${BUILD_NUMBER}
                    '''
                    }
                }
                
            }
        }
        stage('deploy') {
            steps {
                echo 'deploying to localhost'
                script{
                    withCredentials([file(credentialsId: 'kube-config', variable: 'KUBECONFIG')]) {
                    sh '''
                        mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                        cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                        rm -f Deployment/deploy.yaml.tmp
                        kubectl apply -f Deployment --kubeconfig ${KUBECONFIG}

                    '''
                }
            }
        }
    }
}
