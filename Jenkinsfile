pipeline {
    agent any

    parameters {

        string(name: 'CHG_TICKET_NUMBER', defaultValue: 'SPG-13413', description: 'Change Ticket Number', trim: true)
        string(name: 'DEPLOY_TARGET', defaultValue: 'uat', description: 'Select Deploy Target Environment')
        string(name: 'FIX_VERSION', defaultValue: '1.0', description: 'Fix version provided by CTB team', trim: true)
        string(name: 'RELEASE_VERSION', defaultValue: '2.0', description: 'Release version provided by CTB team', trim: true)
    }

    stages {
        stage('Download YAML') {
            steps {
                sh 'curl -O https://raw.githubusercontent.com/shaganga/github_actions/main/releases.yaml'
                  sh "cat ${params.DEPLOY_TARGET}-deploy.xml"
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
                      sh "cat ${params.DEPLOY_TARGET}-undeploy.xml"
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
    xml.append('    <placement>\n')
    yaml.each { block ->
        xml.append("    <package key=\"${block.package_group}:${block.package_name}:${block.oldVersion}\" />\n")
    }

    xml.append("    <agent name=\"{{ lps_agent }}\" />\n")
    xml.append('    </placement>\n')
    xml.append('</undeploy>\n')

    return xml.toString()
}
