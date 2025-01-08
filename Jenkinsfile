pipeline {
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Select the action to perform')
    } 
    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION = 'ap-northeast-3'
    }

    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/Suchismita14/expt.git', branch: 'main'
            }
        }

        stage('Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform validate'
                    sh 'terraform plan'
                }
            }
        }

        stage('Apply/Destroy') {
            steps {
                dir('terraform') {
                    sh "terraform ${params.action} ${params.autoApprove ? '--auto-approve' : ''}"
                }
            }
        }

        stage('Run Ansible Playbook') {
            when {
                expression { params.action == 'apply' }
            }
            steps {
                script {
                    echo "Running Ansible Playbook..."
                    sh 'ansible-playbook -i aws_ec2.yaml playbook.yml'
                }
            }
        }
    }
}
