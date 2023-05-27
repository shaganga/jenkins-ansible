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
                    def xml = new groovy.xml.StreamingMarkupBuilder().bind {
                        deploy {
                            placement {
                                yaml.each { block ->
                                    addPackage(key: "${block.package_group}:${block.package_name}:${block.newVersion}")
                                }
                                agent(name: '{{ lps_agent }}')
                            }
                        }
                    }.toString()

                    writeFile file: "${params.DEPLOY_TARGET}-deploy.xml", text: xml
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    def yaml = readYaml file: 'releases.yaml'
                    def xml = new groovy.xml.StreamingMarkupBuilder().bind {
                        undeploy {
                            placement {
                                yaml.each { block ->
                                    addPackage(key: "${block.package_group}:${block.package_name}:${block.oldVersion}")
                                }
                                agent(name: '{{ lps_agent }}')
                            }
                        }
                    }.toString()

                    writeFile file: "${params.DEPLOY_TARGET}-undeploy.xml", text: xml
                }
            }
        }
    }
}
