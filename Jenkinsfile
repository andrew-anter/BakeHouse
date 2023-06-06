pipeline {
    agent {
    node {
        label 'jenkins-ubuntu-slave'
        }
    }
    stages {
        stage('build') {
            steps {
                echo 'building...'
                echo "${BRANCH_NAME}"

                script{
                    if( env.BRANCH_NAME == "master" ){
                        withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''    
                            docker login -u ${USERNAME} -p ${PASSWORD}
                            docker build . -t andrewanter/bakehouse:v${BUILD_NUMBER}
                            docker push andrewanter/bakehouse:v${BUILD_NUMBER}
                            echo ${BUILD_NUMBER} > ../build.txt
                        '''
                        }
                    }
                }
                
            }
        }
        stage('deploy') {
            steps {
                echo 'deploying to localhost'

                script{

                    if( env.BRANCH_NAME == "master" ){
                        withCredentials([file(credentialsId: 'kube-config', variable: 'KUBECONFIG')]) {
                        sh '''
                            export BUILD_NUMBER=${cat ../build.txt}
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
    }
}
