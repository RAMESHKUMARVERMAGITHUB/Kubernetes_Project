node {
    stage('Checkout') {
        git 'https://github.com/RAMESHKUMARVERMAGITHUB/Kubernetes_Project.git'
    }
    stage('sending docker file to Ansible server over ssh'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237'
            sh 'scp /var/lib/jenkins/workspace/pipeline-job/* ubuntu@172.31.37.237:/home/ubuntu/'
        }
    }
    // stage('Docker Build Image'){
    //     sshagent(['ansible_demo']) {
    //         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 cd /home/ubuntu/'
    //         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
    //     }
    // }
    // stage('Docker Image tagging'){
    //     sshagent(['ansible_demo']) {
    //         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 cd /home/ubuntu/'
    //         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker tag $JOB_NAME:v1.$BUILD_ID rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
    //     }
    // }
    stage('Docker Image Build,tag and push to DockerHub'){
        sshagent(['ansible_demo']) {
            withCredentials([string(credentialsId: 'dockerhub_passwd', variable: 'dockerhub_passwd')]) {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 cd /home/ubuntu/'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker image build -t rameshkumarverma/$JOB_NAME:v1.$BUILD_ID .'
                // sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker tag $JOB_NAME:v1.$BUILD_ID rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
                sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker login -u rameshkumarverma -p ${dockerhub_passwd}"
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker push rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 docker rmi rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
            }
        }
    }
    stage('copy files from ansible to Kubernetes server'){
        sshagent(['kubernetes_server']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.26.161'
            sh 'scp /var/lib/jenkins/workspace/pipeline-job/* ubuntu@172.31.26.161:/home/ubuntu/'
        }
    }
    stage('Kubernetes Deployment using Ansible'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.37.237 ansible-playbook ansible.yml'
        }
    }
}
