pipeline {
    agent any  
    stages {
        stage('build') {
            steps {
                       withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           sh """
                                docker login -u $USERNAME -p $PASSWORD
                                docker build -t bojy1297/bakehouse:${BUILD_NUMBER} .
                                docker push bojy1297/bakehouse:${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../bakehouse-build-number.txt
                           """
                       }
                    }
                }
        stage('deploy') {
            steps {
                            withCredentials([file(credentialsId: 'accesscluster', variable: 'config')]) {
                          sh """
                              export BUILD_NUMBER=\$(cat ../bakehouse-build-number.txt)
                              mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                              cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                              rm -f Deployment/deploy.yaml.tmp
                              gcloud auth activate-service-account --key-file=${config}
                              gcloud container clusters get-credentials private-standerd-gke-cluster --zone us-central1-b --project abdallah-iti-377721
                              kubectl apply -f Deployment
                            """
                        }
                    }
                }
        }
}