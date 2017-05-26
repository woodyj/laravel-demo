#!/usr/bin/env groovy

node {
    // ------------------------------------------
    // Notify devops this build has started.
    // ------------------------------------------
    // slackSend color: '#4CAF50', channel: '#devops', message: "Started ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>)"

    try {

        // =====================================================================
        // Prepare workspace for this build.
        // =====================================================================
    	stage("prepare") {
    		checkout scm
    	}

        // =====================================================================
        // Build project dependencies.
        // =====================================================================
        stage("composer_install") {
            sh 'composer install'
        }

        // =====================================================================
        // Run PHP unit tests.
        // =====================================================================
        stage("phpunit") {
            sh 'vendor/bin/phpunit'
        }

        // =====================================================================
        // IF THIS BRANCH IS MASTER OR DEVELOP, run integration tests
        // =====================================================================
        if (["master", "develop"].contains(env.BRANCH_NAME)) {
            stage("integration_tests") {
                // sh 'vendor/bin/behat'
            }
        }

        // =====================================================================
        // Create new deployment assets.
        // =====================================================================
        switch (env.BRANCH_NAME) {
            case "master":
                stage("codedeploy") {
                    // sh "aws deploy push --application-name My_App_Production --s3-location s3://my-app-production/build-${env.BUILD_NUMBER}.zip"
                }
                break
            case "develop":
                stage("codedeploy") {
                    // sh "aws deploy push --application-name My_App_Staging --s3-location s3://my-app-staging/build-${env.BUILD_NUMBER}.zip"
                }
                break
            default:
                // No deployments for other branches.
                break
        }

        // Notify devops of build failure.
        // slackSend color: '#4CAF50', channel: '#devops', message: "Completed ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>) successfully"
    } catch (all) {
        // ------------------------------------------
        // Notify devops of build success.
        // ------------------------------------------
        // slackSend color: '#f44336', channel: '#devops', message: "Failed ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>) - <${env.BUILD_URL}console|click here to see the console output>"
    }

    // =====================================================================
    // Clean out the workspace directory, ready for next build.
    // =====================================================================
    stage('cleanup') {
	    deleteDir()
	}
}