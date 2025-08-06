pipeline {
  agent any

  environment {
    TF_DIR = 'terraform'
    ANSIBLE_DIR = 'ansible'
    AWS_REGION = 'eu-central-1' // or your region
  }

  stages {
    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/veliJJ/Project-02.git', branch: 'main'
      }
    }

    stage('Terraform Init & Apply') {
      steps {
        dir("${env.TF_DIR}") {
          withCredentials([usernamePassword(credentialsId: 'aws-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
            sh '''
              terraform init
              terraform apply -auto-approve
            '''
          }
        }
      }
    }

    stage('Run Ansible Playbook') {
      steps {
        dir("${env.ANSIBLE_DIR}") {
          withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'KEY_PATH')]) {
            // Assume Terraform outputs IP to a file or stdout
            sh '''
              INSTANCE_IP=$(cd ../terraform && terraform output -raw instance_ip)
              ansible-playbook -i "$INSTANCE_IP," playbook.yml --private-key $KEY_PATH -u ec2-user
            '''
          }
        }
      }
    }
  }
}

