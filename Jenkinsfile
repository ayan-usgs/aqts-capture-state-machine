@Library(value='iow-ecs-pipeline@1.0.0', changelog=false) _

pipeline {
    agent {
        node {
            label 'project:any'
        }
    }
    parameters {
        choice(choices: ['test', 'qa', 'prod-external'], description: 'Deploy Stage (i.e. tier)', name: 'DEPLOY_STAGE')
    }
    triggers {
        pollSCM('H/5 * * * *')
    }
    stages {
        stage('run build the zip file for lambda') {
            agent {
                dockerfile true
            }
            steps {
                sh '''
                npm install serverless
                ./node_modules/serverless/bin/serverless plugin install -n serverless-step-functions
                ./node_modules/serverless/bin/serverless deploy --stage ${DEPLOY_STAGE}
                '''
            }
        }
    }
    post {
        always {
            script {
                pipelineUtils.cleanWorkspace()
            }
        }
        failure {
            script {
                pipelineUtils.sendEmailNotification(
                    to: 'ayan@usgs.gov',
                    attachLog: true
                )
            }
        }
    }
}
