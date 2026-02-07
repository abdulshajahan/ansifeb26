pipeline {
    agent any
    
    environment {
        ANSIBLE_CONFIG = "${WORKSPACE}/ansible.cfg"
        ANSIBLE_FORCE_COLOR = 'true'
    }
    
    parameters {
        string(
            name: 'PLAYBOOK',
            defaultValue: '3.diskreport.yaml',
            description: 'Playbook to execute'
        )
        
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'ls -la'
            }
        }
        
        stage('Setup Ansible') {
            steps {
                sh '''
                    echo "=== Ansible Version ==="
                    ansible --version
                    echo "=== Inventory Preview ==="
                    cat inventory.ini || true
                '''
            }
        }
        
        stage('Run Ansible Playbook') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ansible-devops',
                        keyFileVariable: 'SSH_KEY'
                    )
                ]) {
                    sh '''
                        export ANSIBLE_HOST_KEY_CHECKING=False
                        
                        ansible-playbook \
                          -i inventory.ini \
                          ${PLAYBOOK} \
                          --private-key=${SSH_KEY}
                    '''
                }
            }
        }
        
        stage('Validate Deployment') {
            steps {
                sh '''
                    echo "=== Playbook Execution Complete ==="
                    echo "Check deployment at /tmp on each node"
                '''
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}