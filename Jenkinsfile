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
                sh """
                curl -o ${params.DEPLOY_TARGET}.yml https://raw.githubusercontent.com/shaganga/github_actions/main/.github/workflows/blank.yml
                """
            }
        }
        stage('Run ansible') {
            steps {
                ansiblePlaybook(
                    playbook: 'deploy.yml',
                    inventory: 'inventory.ini',
                    extras: "-e @${params.DEPLOY_TARGET}.yml -e RELEASE_VERSION=${params.RELEASE_VERSION} -e FIX_VERSION=${params.FIX_VERSION}"
                )
            }
        }
    }
}
