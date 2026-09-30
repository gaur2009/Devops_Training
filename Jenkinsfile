pipeline {
    agent any

    stages {
stage('Version Info') {
    steps {
        script {
            def appVersion = readFile('version.txt').trim()

            echo "================================"
            echo "Application Version : ${appVersion}"
            echo "Git Branch          : ${env.BRANCH_NAME}"
            echo "Git Commit          : ${env.GIT_COMMIT}"
            echo "Jenkins Build       : ${env.BUILD_NUMBER}"
            echo "================================"
        }
    }
}
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
