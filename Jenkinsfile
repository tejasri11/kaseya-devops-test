pipeline {
  agent any
    stages {
        stage('Build Parameters') {
            steps {
                script {
                    properties([
                        parameters([
                            string(
                                defaultValue: '',
                                name: 'ansibleuser',
                                trim: true
                            ),
                            string(
                                defaultValue: '',
                                name: 'password',
                                trim: true
                            ),
                            string(
                                defaultValue: '',
                                name: 'windowshost',
                                trim: true
                            )
                        ])
                    ])
                }
            }
        }
        stage ('Add host to inventory') {
            steps {
              sh "sed -i '/^\[win_vm\].*/a ${windowshost}' inv"
            }
        }
        stage ('Windows Server Update via ansible') {
            steps {
            ansiblePlaybook(installation: "ansible2.8.6",
									inventory: "inv",
									playbook: "playbook.yaml",
									extraVars: [
									       ansible_user: [value: "${ansibleuser}", hidden: false],
                         ansible_password: [value: "${password}", hidden: true]
									],
									colorized: true)
            }
        }

    }
 }
