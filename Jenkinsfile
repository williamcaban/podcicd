pipeline {
    agent any

    options {
        // set a timeout of 5 minutes for this pipeline
        timeout(time: 5, unit: 'MINUTES')
    } //options

    environment {
        APP_NAME    = "podcicd"
        GIT_REPO    = "https://github.com/williamcaban/podcicd.git"
        GIT_BRANCH  = "master"
        CONTEXT_DIR = "myapp"
        CURR_BUILD  = "${currentBuild.number}"
        PREV_BUILD  = "${currentBuild.previousBuild.getNumber()}"
        BUILD_CAUSE = "${currentBuild.rawBuild.getCauses()}"

        CICD_PRJ    = "cicd"
        CICD_DEV    = "${CICD_PRJ}"+"-dev"
        CICD_PROD   = "${CICD_PRJ}"+"-prod"
        CICD_STAGE  = "${CICD_PRJ}"+"-staging"
    }

    stages {
            stage('Build') {
                steps {
                    echo "Sample Build stage using project ${CICD_DEV}"
                    script {
                        openshift.withCluster() {
                            openshift.withProject("${CICD_DEV}")
                            {

                                if (openshift.selector("bc",APPLICATION_NAME).exists()) {
                                    echo "Using existing BuildConfig. Running new Build"
                                    def bc = openshift.startBuild(APPLICATION_NAME)
                                    openshift.set("env dc/${APPLICATION_NAME} BUILD_NUMBER=${CURR_BUILD}")
                                    // output build logs to the Jenkins conosole
                                    echo "Logs from build"
                                    def result = bc.logs('-f')
                                    // actions that took place
                                    echo "The logs operation require ${result.actions.size()} 'oc' interactions"
                                    // see exactly what oc command was executed.
                                    echo "Logs executed: ${result.actions[0].cmd}"
                                } else {
                                    echo "No proevious BuildConfig. Creating new BuildConfig."
                                    def myNewApp = openshift.newApp (
                                        "${GIT_REPO}#${GIT_BRANCH}", 
                                        "--name=${APPLICATION_NAME}", 
                                        "--context-dir=${CONTEXT_DIR}", 
                                        "-e BUILD_NUMBER=${CURR_BUILD}", 
                                        "-e BUILD_ENV=${openshift.project()}"
                                        )
                                    echo "new-app myNewApp ${myNewApp.count()} objects named: ${myNewApp.names()}"
                                    myNewApp.describe()
                                    // selects the build config 
                                    def bc = myNewApp.narrow('bc')
                                    // output build logs to the Jenkins conosole
                                    echo "Logs from build"
                                    def result = bc.logs('-f')
                                    // actions that took place
                                    echo "The logs operation require ${result.actions.size()} 'oc' interactions"
                                    // see exactly what oc command was executed.
                                    echo "Logs executed: ${result.actions[0].cmd}"
                                } //else

                                echo "Tag Container image with 'build number' as version"
                                openshift.tag("${APPLICATION_NAME}:latest", "${APPLICATION_NAME}:v${BUILD_NUMBER}")

                                echo "Validating Route for Service exist, if Not create Route"
                                if (!openshift.selector("route",APPLICATION_NAME).exists()) {
                                    openshift.selector("svc",APPLICATION_NAME).expose()
                                }

                            } // project
                        } // cluster
                    } // script
                } // steps
            } //stage-build

            stage('Test') {

                steps {
                    echo "Test Service is operational and responding"
                    script {
                        openshift.withCluster() {
                                openshift.withProject() {
                                    def check_service = openshift.verifyService(${APPLICATION_NAME})
                                    if (check_service) {
                                        echo "Able to connect to ${APPLICATION_NAME}"
                                    } else {
                                        echo "Unable to connect to ${APPLICATION_NAME}"
                                        currentBuild.result = "TEST_FAIL"
                                        return
                                    }
                                } // withProject
                        } // withCluster
                    } // script
                } // steps
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
