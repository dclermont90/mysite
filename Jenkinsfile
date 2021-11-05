#!/usr/bin/env groovy

node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:devopstraineeorg/mysite.git'

            // Start services (Let docker-compose build containers for testing)
            sh "./develop up -d"

            // Get composer dependencies
            sh "./develop composer install"

	    // Create .env file for testing
            sh '/var/lib/jenkins/.venv/bin/aws s3 cp --endpoint=https://fra1.digitaloceanspaces.com s3://dominique-test-space/.env-ci ./.env-ci'
            sh './develop art key:generate'
	 }

        stage('test') {
            sh "APP_ENV=testing ./develop test"
        }

        if( ${BRANCH_NAME} == 'master' ) {
            stage('package') {
                sh './docker/build'
            }
        }
    } catch(error) {
        // Maybe some alerting?
        throw error
    } finally {
        // Spin down containers no matter what happens
        sh './develop down'
    }
}
