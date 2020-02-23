pipeline {
  environment {
    registry = "docker_hub_account/repository_name"
    registryCredential = 'dockerhub'
  }
  agent any
  stages {
    stage('Building image') {
      steps{
	sh '''#!/bin/bash
                sudo docker build -t app1:latest .
        '''
      }
    }
    stage('Push to repository') {
      steps{
	sh '''#!/bin/bash
                sudo $(aws ecr get-login --no-include-email --region us-east-1)
    sudo docker tag app1:latest ${ECR_ADDRESS}:latest
    sudo docker push ${ECR_ADDRESS}:latest
        '''
      }
    }
    stage('Smart Check') {
      steps{
	sh '''#!/bin/bash
		/bin/scan.sh
        '''
      }
    }
    stage('Deploy'){
      steps{
	sh '''#!/bin/bash
		kubectl --kubeconfig=kubeconfig delete deployment app1 ; sleep 10 ;  ( kubectl --kubeconfig=kubeconfig  create -f pod.yml  && kubectl --kubeconfig=kubeconfig expose deployment  app1  --type=LoadBalancer --port 80 --target-port 80 )
               sleep 15
               kubectl --kubeconfig=kubeconfig get services app1
        '''
      }
    }
  }
}
