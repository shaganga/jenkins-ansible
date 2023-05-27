def deployMode = ['Mode1', 'Mode2', 'Mode3']

pipeline {
    agent any

    parameters {
        choice(name: 'DEPLOY_MODE', choices: deployMode, description: 'Deploy mode')
        string(name: 'CHG_TICKET_NUMBER', defaultValue: '', description: 'Change Ticket Number', trim: true)
        choice(name: 'DEPLOY_TARGET', choices: getEnvironments, description: 'Select Deploy Target Environment')
        string(name: 'FIX_VERSION', defaultValue: '', description: 'Fix version provided by CTB team', trim: true)
        string(name: 'RELEASE_VERSION', defaultValue: '', description: 'Release version provided by CTB team', trim: true)
    }

    stages {
        stage('Download YAML') {
            steps {
                sh 'curl -O https://raw.githubusercontent.com/shaganga/github_actions/main/releases.yaml'
            }
        }

        stage('Generate Deploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'releases.yaml'
                    def deployXml = generateDeployXml(yaml)
                    writeFile file: "${params.DEPLOY_TARGET}-deploy.xml", text: deployXml
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'releases.yaml'
                    def undeployXml = generateUndeployXml(yaml)
                    writeFile file: "${params.DEPLOY_TARGET}-undeploy.xml", text: undeployXml
                }
            }
        }
    }
}

def generateDeployXml(yaml) {
    def xml = new StringBuilder()
    xml.append('<deploy>\n')
    xml.append('    <placement>\n')

    yaml.each { block ->
        xml.append("        <package key=\"${block.package_group}:${block.package_name}:${block.newVersion}\" />\n")
    }

    xml.append("        <agent name=\"{{ lps_agent }}\" />\n")
    xml.append('    </placement>\n')
    xml.append('</deploy>\n')

    return xml.toString()
}

def generateUndeployXml(yaml) {
    def xml = new StringBuilder()
    xml.append('<undeploy>\n')

    yaml.each { block ->
        xml.append("    <package key=\"${block.package_group}:${block.package_name}:${block.oldVersion}\" />\n")
    }

    xml.append("    <agent name=\"{{ lps_agent }}\" />\n")
    xml.append('</undeploy>\n')

    return xml.toString()
}
