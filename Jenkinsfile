pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME}"
                sh '''
                    mkdir -p build
                    cp index.html build/
                    echo "Build completed successfully"
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running tests"
                sh '''
                    test -f build/index.html
                    echo "Tests passed successfully"
                '''
            }
        }

        stage('Deploy DEV') {
            when {
                branch 'develop'
            }
            steps {
                echo "Deploying DEVELOP branch to DEV"
            }
        }

        stage('Deploy PROD') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploying MAIN branch to PROD"
            }
        }
    }
}
