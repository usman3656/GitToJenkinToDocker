pipeline {
    agent any
    tools{
        maven 'maven_3_8_4'
    }
	environment {
        dotenv '.env'
        KUBECONFIG = "${KUBECONFIG_PATH}"
        DOCKERHUB_CREDENTIALS = "${DOCKERHUB_CREDENTIALS}"
    }

    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/usman3656/GitToJenkinToDocker']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t usmanghanibawany/devops-integration .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'env.DOCKERHUB_CREDENTIALS', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u usmanghanibawany -p ${dockerhubpwd}'

}
                   sh 'docker push usmanghanibawany/devops-integration'
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'env.KUBECONFIG')
                }
            }
        }
    }
}