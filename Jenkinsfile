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
                   // sh "curl -O https://raw.githubusercontent.com/shaganga/github_actions/main/release.yaml"
                   // sh "mv release.yaml ${params.DEPLOY_TARGET}.yml"
                    sh "curl -O https://raw.githubusercontent.com/shaganga/github_actions/main/releases.yaml"
                    
                }
            }
        }
             stage('Convrt YML to XML - deploy') {
            steps {
                script {
                  sh "rm -rf ${params.DEPLOY_TARGET}-deploy.yml"
                  //  sh "curl -O https://raw.githubusercontent.com/shaganga/github_actions/main/releases.yaml"
                   sh "mv releases.yaml ${params.DEPLOY_TARGET}-deploy.yml"
                   sh "cat ${params.DEPLOY_TARGET}-deploy.yml"
             
                    sh """
                    echo '<deploy>' > ${params.DEPLOY_TARGET}-deploy.xml
                    cat uat-deploy.yml |
                    sed 's/- package_group:/    <placement>\\n        <package key="{{ package_group/g' |
                    sed 's/- package_name:/        {{ package_name/g' |
                    sed 's/newVersion:/ }}:{{ newVersion/g' |
                    sed 's/oldVersion:/ }}" \\/>\\n    <\\/placement>/g' >> ${params.DEPLOY_TARGET}-deploy.xml
                    echo '    <agent name="{{ lps_agent }}" />' >> ${params.DEPLOY_TARGET}-deploy.xml
                    echo '</deploy>' >> ${params.DEPLOY_TARGET}-deploy.xml
                    """
                   
                }
            }
        }
        
        //stage('Run ansible') {
        //    steps {
         //       ansiblePlaybook(
           //         playbook: 'deploy.yml', 
           //         inventory: 'inventory.ini',
            //        extras: "-e DEPLOY_TARGET=${params.DEPLOY_TARGET} -e @${params.DEPLOY_TARGET}.yml -e RELEASE_VERSION=${params.RELEASE_VERSION} -e FIX_VERSION=${params.FIX_VERSION}"
               // )
          //  }
        //}
    }
}
