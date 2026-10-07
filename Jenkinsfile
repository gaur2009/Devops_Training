pipeline {

    agent any

    parameters {

        string(
            name: 'VERSION_NUMBER',
            defaultValue: '1.1.0',
            description: 'Application version to deploy'
        )

        choice(
            name: 'ENVIRONMENT',
            choices: ['DEV', 'STG', 'PRD'],
            description: 'Select target deployment environment'
        )
    }

    stages {

        stage('Pipeline Information') {
            steps {
                echo "================================"
                echo "Application Version : ${params.VERSION_NUMBER}"
                echo "Environment         : ${params.ENVIRONMENT}"
                echo "Git Branch          : ${env.BRANCH_NAME}"
                echo "Git Commit          : ${env.GIT_COMMIT}"
                echo "Jenkins Build       : ${env.BUILD_NUMBER}"
                echo "================================"
            }
        }

        stage('Build') {
            steps {
                echo "Building application..."
                sh '''
                    mkdir -p build
                    cp index.html build/
                    echo "Build completed successfully"
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."

                sh '''
                    test -f build/index.html
                    echo "Tests passed successfully"
                '''
            }
        }

        stage('Deploy') {
            steps {
                script {

                    if (params.ENVIRONMENT == 'DEV') {
                        echo "Deploying version ${params.VERSION_NUMBER} to DEV"
                    }

                    else if (params.ENVIRONMENT == 'STG') {
                        echo "Deploying version ${params.VERSION_NUMBER} to STG"
                    }

                    else if (params.ENVIRONMENT == 'PRD') {
                        echo "Deploying version ${params.VERSION_NUMBER} to PRD"
                    }
                }
            }
        }
    }

    post {

        success {
            echo "Deployment completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }
    }
}
