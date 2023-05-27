pipeline {
    agent any

    parameters {
        string(name: 'DEPLOY_TARGET', defaultValue: 'uat', description: 'Deployment target')
        string(name: 'RELEASE_VERSION', defaultValue: '1.0.0', description: 'Release version')
        string(name: 'FIX_VERSION', defaultValue: '1.0.1', description: 'Fix version')
    }

    stages {
        stage('Download code') {
            steps {
                script {
                    sh "curl -O https://raw.githubusercontent.com/shaganga/github_actions/main/releases.yaml"
                }
            }
        }

        stage('Generate Deploy XML') {
            steps {
                script {
                    sh """
                    echo '<deploy>' > ${params.DEPLOY_TARGET}-deploy.xml
                    cat uat-deploy.yml | while IFS= read -r line
                    do
                        if [[ \$line == *"package_group:"* ]]
                        then
                            package_group=\$(echo \$line | awk -F '\"' '{print \$2}')
                        elif [[ \$line == *"package_name:"* ]]
                        then
                            package_name=\$(echo \$line | awk -F '\"' '{print \$2}')
                        elif [[ \$line == *"newVersion:"* ]]
                        then
                            newVersion=\$(echo \$line | awk -F '\"' '{print \$2}')
                            echo "    <placement>" >> ${params.DEPLOY_TARGET}-deploy.xml
                            echo "        <package key=\"\$package_group:\$package_name:\$newVersion\" />" >> ${params.DEPLOY_TARGET}-deploy.xml
                            echo "        <agent name=\"{{ lps_agent }}\" />" >> ${params.DEPLOY_TARGET}-deploy.xml
                            echo "    </placement>" >> ${params.DEPLOY_TARGET}-deploy.xml
                        fi
                    done
                    echo '</deploy>' >> ${params.DEPLOY_TARGET}-deploy.xml
                    """
                    sh "cat ${params.DEPLOY_TARGET}-deploy.xml"
                }
            }
        }

        stage('Generate Undeploy XML') {
            steps {
                script {
                    sh """
                    echo '<undeploy>' > ${params.DEPLOY_TARGET}-undeploy.xml
                    cat uat-deploy.yml | while IFS= read -r line
                    do
                        if [[ \$line == *"package_group:"* ]]
                        then
                            package_group=\$(echo \$line | awk -F '\"' '{print \$2}')
                        elif [[ \$line == *"package_name:"* ]]
                        then
                            package_name=\$(echo \$line | awk -F '\"' '{print \$2}')
                        elif [[ \$line == *"oldVersion:"* ]]
                        then
                            oldVersion=\$(echo \$line | awk -F '\"' '{print \$2}')
                            echo "    <placement>" >> ${params.DEPLOY_TARGET}-undeploy.xml
                            echo "        <package key=\"\$package_group:\$package_name:\$oldVersion\" />" >> ${params.DEPLOY_TARGET}-undeploy.xml
                            echo "        <agent name=\"{{ lps_agent }}\" />" >> ${params.DEPLOY_TARGET}-undeploy.xml
                            echo "    </placement>" >> ${params.DEPLOY_TARGET}-undeploy.xml
                        fi
                    done
                    echo '</undeploy>' >> ${params.DEPLOY_TARGET}-undeploy.xml
                    """
                       sh "cat ${params.DEPLOY_TARGET}-undeploy.xml"
                }
            }
        }
    }
}
