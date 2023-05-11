pipeline {
    agent any

    parameters {
        string(name: 'DEPLOY_TARGET', defaultValue: 'test', description: 'Deployment target')
        string(name: 'RELEASE_VERSION', defaultValue: '1.0.0', description: 'Release version')
        string(name: 'FIX_VERSION', defaultValue: '1.0.1', description: 'Fix version')
    }

    stages {
        stage('Download code') {
            steps {
                script {
                    def deployTargetFileContent = """
                    ---
                    RELEASE_VERSION: ${params.RELEASE_VERSION}
                    FIX_VERSION: ${params.FIX_VERSION}
                    """
                    writeFile file: "${params.DEPLOY_TARGET}.yml", text: deployTargetFileContent
                }
            }
        }
        stage('Run ansible') {
            steps {
                ansiblePlaybook(
                    playbook: 'deploy.yml',
                    inventory: 'inventory.ini',
                    extras: "-e DEPLOY_TARGET=${params.DEPLOY_TARGET} -e @${params.DEPLOY_TARGET}.yml"
                )
            }
        }
    }
}
