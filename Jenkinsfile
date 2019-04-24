pipeline {
    agent any

    options {
        // set a timeout of 5 minutes for this pipeline
        timeout(time: 5, unit: 'MINUTES')
    } //options

    environment {
        GIT_URL     = "https://github.com/williamcaban/podcicd.git"
        GIT_BRANCH  = "master"
        CONTEXT_DIR = "myapp"
        CURR_BUILD  = ${currentBUild.number}
        PREV_BUILD  = ${currentBuild.previousBuild.getNumber()}
        BUILD_CAUSE = ${currentBuild.rawBuild.getCauses()}

        CICD_PRJ    = "cicd"
        CICD_DEV    = ${CICD_PRJ}+"-dev"
        CICD_PROD   = ${CICD_PRJ}+"-prod"
        CICD_STAGE  = ${CICD_PRJ}+"-staging"
    }

    stages {
            stage('Build') {
                steps {
                    echo "Sample Build stage ..."
                    script {
                        openshift.withCluster() {
                            openshift.withProject(${CICD_DEV})
                            {
                                def myapp = openshift.newApp ("${GIT_URL}#${BRANCH}"", "--context-dir=${CONTEXT_DIR}", "-p BUILD_NUMBER=${CURR_BUILD}")

                            } // project
                        } // cluster
                    } // script
                } // steps
            } //stage-build

            stage('Test') {
                steps {
                    echo "Sample Test stage ..."
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
