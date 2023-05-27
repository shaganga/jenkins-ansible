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
                    def xml = new StringWriter()
                    xml.echo('<deploy>')
                    xml.echo('    <placement>')
                    yaml.each { block ->
                        def packageGroup = block.package_group
                        def packageName = block.package_name
                        def newVersion = block.newVersion
                        xml.echo('        <package key="' + packageGroup + ':' + packageName + ':' + newVersion + '" />')
                    }
                    xml.echo('        <agent name="{{ lps_agent }}" />')
                    xml.echo('    </placement>')
                    xml.echo('</deploy>')
                    writeFile file: "${params.DEPLOY_TARGET}-deploy.xml", text: xml.toString()
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'releases.yaml'
                    def xml = new StringWriter()
                    xml.echo('<undeploy>')
                    xml.echo('    <placement>')
                    yaml.each { block ->
                        def packageGroup = block.package_group
                        def packageName = block.package_name
                        def oldVersion = block.oldVersion
                        xml.echo('        <package key="' + packageGroup + ':' + packageName + ':' + oldVersion + '" />')
                    }
                    xml.echo('        <agent name="{{ lps_agent }}" />')
                    xml.echo('    </placement>')
                    xml.echo('</undeploy>')
                    writeFile file: "${params.DEPLOY_TARGET}-undeploy.xml", text: xml.toString()
                }
            }
        }
    }
}
