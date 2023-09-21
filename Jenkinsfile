pipeline {
    //agent{
        //label 'nodejs'
    //}
    environment {
        DOCKER_TAG = "v${env.BUILD_NUMBER}"
    }
    options{
        // Stop the build early in case of compile or test failures
        skipStagesAfterUnstable()
    }
    stages {
        
        stage("Build Project"){
            steps{
                sh label: '', script: '''mvn clean install'''

            }
        }
        stage('SCA - Fossa Pilot - Scan') {
            agent { label 'fossa' }
            when {
                expression {
                    pconfig.getWantsUseFossaPilot()
                }
            }
            steps {
                script {
                    def shell = pipelineUtilities.getShellCommand()


                    //pre-scan setup - just clone. Fossa doesn't need build tool setup.
                    pipelineUtilities.cleanWorkspaceAndCheckoutSourceCode(pconfig)

                    withCredentials([string(credentialsId: pipelineConstants.FOSSA_API_KEY_NAME, variable: 'fossaApiKey')]) {
                        env['FOSSA_API_KEY'] = fossaApiKey
                    }

                    "${shell}"("fossa list-targets")

                    "${shell}"("fossa analyze" +
                            " --title " + pconfig.getProjectVersion() +
                            " --release-group-name 'fossa-pilot-synopsys-io-pipeline'" +
                            " --release-group-release '1.0' " +
                            " --debug")
                }
            }
        }
        
        
    }
} 
