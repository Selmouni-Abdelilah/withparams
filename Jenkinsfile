pipeline {
    agent any
    parameters {
        choice(name: "VM_CHOICE", choices: ["Linux VM","Windows VM"], description: "Select the VM type to import")
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHubcredentials', url: 'https://github.com/Selmouni-Abdelilah/withparams']])
                }
            }
        }
        stage('Azure login'){
            steps{
                withCredentials([azureServicePrincipal('Azure_credentials')]) {
                    sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                }
            }
        }
        stage('Terraform ') {
            steps {
                script {
                    withCredentials([azureServicePrincipal(credentialsId: 'Azure_credentials',
                                        subscriptionIdVariable: 'SUBS_ID',
                                        clientIdVariable: 'CLIENT_ID',
                                        clientSecretVariable: 'CLIENT_SECRET',
                                        tenantIdVariable: 'TENANT_ID')]) {
                        sh 'terraform init -upgrade'
                        if (params.VM_CHOICE == "Linux VM") {
                            sh "terraform import azurerm_linux_virtual_machine.linux /subscriptions/${SUBS_ID}/resourceGroups/rg_abdel_proc/providers/Microsoft.Compute/virtualMachines/LinuxVM"
                        }
                        else {
                            sh "terraform import azurerm_windows_virtual_machine.windows /subscriptions/${SUBS_ID}/resourceGroups/rg_abdel_proc/providers/Microsoft.Compute/virtualMachines/windows-VM"
                        }
                    }
                }
            }
        }
    }
}