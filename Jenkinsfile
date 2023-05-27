import hudson.util.XmlStringBuilder

pipeline {
    agent any

    parameters {
        string(name: 'DEPLOY_TARGET', defaultValue: 'uat', description: 'Deployment target')
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
                    def xml = new XmlStringBuilder()
                    xml.append('<deploy>')
                    xml.append('    <placement>')
                    yaml.each { block ->
                        def packageGroup = block.package_group
                        def packageName = block.package_name
                        def newVersion = block.newVersion
                        xml.append('        <package key="' + packageGroup + ':' + packageName + ':' + newVersion + '" />')
                    }
                    xml.append('        <agent name="{{ lps_agent }}" />')
                    xml.append('    </placement>')
                    xml.append('</deploy>')
                    writeFile file: "${params.DEPLOY_TARGET}-deploy.xml", text: xml.toString()
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'releases.yaml'
                    def xml = new XmlStringBuilder()
                    xml.append('<undeploy>')
                    xml.append('    <placement>')
                    yaml.each { block ->
                        def packageGroup = block.package_group
                        def packageName = block.package_name
                        def oldVersion = block.oldVersion
                        xml.append('        <package key="' + packageGroup + ':' + packageName + ':' + oldVersion + '" />')
                    }
                    xml.append('        <agent name="{{ lps_agent }}" />')
                    xml.append('    </placement>')
                    xml.append('</undeploy>')
                    writeFile file: "${params.DEPLOY_TARGET}-undeploy.xml", text: xml.toString()
                }
            }
        }
    }
}
