node {
    // ------------------------------------------
    // Notify devops this build has started.
    // ------------------------------------------
    // slackSend color: '#4CAF50', channel: '#devops', message: "Started ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>)"

    // asdfghjksfsdsdsdsdsdssdssdsdsdd

    try {
        // =====================================================================
        // Build project and its dependencies.
        // =====================================================================
        stage("Build") {
            checkout scm
            sh 'composer install'
        }

        // =====================================================================
        // Run PHP unit tests.
        // =====================================================================
        stage("Unit Tests") {
            sh 'vendor/bin/phpunit'
        }

        // =====================================================================
        // IF THIS BRANCH IS MASTER OR DEVELOP, run integration tests
        // =====================================================================
        if (["master", "develop"].contains(env.BRANCH_NAME)) {
            stage("Integration Tests") {
                // sh 'vendor/bin/behat'
            }
        }

        // =====================================================================
        // Create new deployment assets.
        // =====================================================================
        switch (env.BRANCH_NAME) {
            case "master":
                stage("Deploy: Live") {
                    // sh "aws deploy push --application-name My_App_Production --s3-location s3://my-app-production/build-${env.BUILD_NUMBER}.zip"
                }
                break
            case "develop":
                stage("Deploy: Stage") {
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
        sh 'mkdir -p reports/html'
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'reports/html/', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])

        // ------------------------------------------
        // Notify devops of build success.
        // ------------------------------------------
        // slackSend color: '#f44336', channel: '#devops', message: "Failed ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>) - <${env.BUILD_URL}console|click here to see the console output>"

        // =============================================================================================
        // Ensure this build 'fails', and pipeline execution halts (to prevent accidental deployments).
        // =============================================================================================
        // error('Build failed.')
    }
}