pipeline {
    agent any

    stages {
        stage('Generate Deploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'uat-deploy.yml'
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
                    writeFile file: 'uat-deploy.xml', text: xml.toString()
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'uat-undeploy.yml'
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
                    writeFile file: 'uat-undep
