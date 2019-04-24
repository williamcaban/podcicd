pipeline {
    agent any

    options {
        // set a timeout of 5 minutes for this pipeline
        timeout(time: 5, unit: 'MINUTES')
    } //options

    environment {
    MY_PIPELINE_VAR = "Demo Env Var from Pipeline"
    }

    stages {
            stage('Build') {
                steps {
                    echo "Sample Build stage ..."
                }
            } //stage 

            stage('Test') {
                steps {
                    echo "Sample Test stage with variable from Jenkinsfile >> ${MY_PIPELINE_VAR}"
                }
            } //stage 
            
            stage('Promote') {
                steps {
                    echo "Sample Promote stage with OpenShift Client Plugin DSL"
                    script {
                        openshift.withCluster() {
                            openshift.withProject() {
                                echo "Using project: ${openshift.project()}"
                            }
                        }
                    } // script
                } //steps 
            } //stage

    } // stages
} // pipeline
