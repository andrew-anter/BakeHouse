pipeline {
    agent {
    node {
        label 'jenkins-ubuntu-slave'
        }
    }
    stages {
        stage('build') {
            steps {
                echo "${BRANCH_NAME}"
                script{
                    if( env.BRANCH_NAME == "release" ){
                        withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        echo 'building...'
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
                echo "${BRANCH_NAME}"
                script{
                    if( env.BRANCH_NAME == "dev" || env.BRANCH_NAME == "preprod" || env.BRANCH_NAME == "test" ){
                        withCredentials([file(credentialsId: 'kube-config', variable: 'KUBECONFIG')]) {
                        echo 'deploying to localhost'
                        sh '''
                            export BUILD_NUMBER=$(cat ../build.txt)
                            mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                            cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                            rm -f Deployment/deploy.yaml.tmp
                            kubectl apply -f Deployment --kubeconfig ${KUBECONFIG} -n ${BRANCH_NAME}
                            
                        '''
                        }
                    }
                }   
            }
        }
    }
}
