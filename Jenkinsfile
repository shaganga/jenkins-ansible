pipeline {
    agent any

    stages {
        stage('Generate Deploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'uat-deploy.yml'
                    def xml = new StringWriter()
                    xml.println('<deploy>')
                    xml.println('    <placement>')
                    yaml.each { block ->
                        def packageGroup = block.package_group
                        def packageName = block.package_name
                        def newVersion = block.newVersion
                        xml.println('        <package key="' + packageGroup + ':' + packageName + ':' + newVersion + '" />')
                    }
                    xml.println('        <agent name="{{ lps_agent }}" />')
                    xml.println('    </placement>')
                    xml.println('</deploy>')
                    writeFile file: 'uat-deploy.xml', text: xml.toString()
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'uat-undeploy.yml'
                    def xml = new StringWriter()
                    xml.println('<undeploy>')
                    xml.println('    <placement>')
                    yaml.each { block ->
                        def packageGroup = block.package_group
                        def packageName = block.package_name
                        def oldVersion = block.oldVersion
                        xml.println('        <package key="' + packageGroup + ':' + packageName + ':' + oldVersion + '" />')
                    }
                    xml.println('        <agent name="{{ lps_agent }}" />')
                    xml.println('    </placement>')
                    xml.println('</undeploy>')
                    writeFile file: 'uat-undeploy.xml', text: xml.toString()
                }
            }
        }
    }
}
