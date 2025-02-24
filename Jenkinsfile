pipeline {
    agent any
    
    stages {
        stage('Daily Compliance Run') {
            steps {
                echo 'Running a compliance scan with inspec....'
                script {
                    def remote = [:]
                    remote.name = "controlnode"
                    remote.host = "34.51.35.44"
                    remote.allowAnyHosts = true
                    
                    withCredentials([sshUserPrivateKey(
                        credentialsId: 'sshUser',
                        keyFileVariable: 'identity',
                        passphraseVariable: '',
                        usernameVariable: 'userName'
                    )]) {
                        remote.user = userName
                        remote.identityFile = identity
                        
                        stage("Enforce with Ansible") {
                            // Using git -C to specify working directory
                            sshCommand remote: remote, sudo: true, command: 'git -C /home/GitOps/secops/ansible pull origin'
                            sshCommand remote: remote, sudo: true, command: 'ansible-playbook /home/GitOps/secops/ansible/compliance.yaml'
                        }
                        
                        stage("Scan with InSpec") {
                            sshCommand remote: remote, sudo: true, command: 'inspec exec --no-distinct-exit /root/linux-baseline/'
                        }
                    }
                }
            }
        }
    }
}
