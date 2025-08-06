pipeline {
  agent any

  environment {
    TF_DIR = 'terraform'
    ANSIBLE_DIR = 'ansible'
    PRIVATE_KEY_PATH = "${env.WORKSPACE}/${ANSIBLE_DIR}/terraform-key.pem"
  }

  stages {
    stage('Checkout Terraform (main)') {
      steps {
        dir(TF_DIR) {
          checkout([$class: 'GitSCM', branches: [[name: 'refs/heads/main']], userRemoteConfigs: [[url: 'https://github.com/veliJJ/Project-02.git']]])
        }
      }
    }

    stage('Terraform Apply') {
      steps {
        dir(TF_DIR) {
          sh '''
            terraform init
            terraform apply -auto-approve
            terraform output -raw private_key_pem > ../${ANSIBLE_DIR}/terraform-key.pem
            chmod 600 ../${ANSIBLE_DIR}/terraform-key.pem
          '''
        }
      }
    }

    stage('Checkout Ansible branch') {
      steps {
        dir(ANSIBLE_DIR) {
          checkout([$class: 'GitSCM', branches: [[name: 'refs/heads/ansible']], userRemoteConfigs: [[url: 'https://github.com/veliJJ/Project-02.git']]])
        }
      }
    }

    stage('Run Ansible Playbook') {
      steps {
        dir(ANSIBLE_DIR) {
          sh """
            ansible-playbook -i inventory.ini playbook.yml --private-key=${PRIVATE_KEY_PATH}
          """
        }
      }
    }
  }
}
