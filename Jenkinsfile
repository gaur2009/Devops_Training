pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://localhost:8081'
        NEXUS_REPO = 'jenkins-artifacts'
    }

    stages {
        stage('Version Information') {
            steps {
                script {
                    def version = readFile('version.txt').trim()
                    def commitId = env.GIT_COMMIT.take(8)

                    env.APP_VERSION = version
                    env.RELEASE_ID = "${version}-${commitId}"
                    env.ARTIFACT_NAME = "payment-app-${env.RELEASE_ID}.zip"

                    echo "Application Version: ${env.APP_VERSION}"
                    echo "Release ID: ${env.RELEASE_ID}"
                    echo "Git Branch: ${env.BRANCH_NAME}"
                    echo "Git Commit: ${env.GIT_COMMIT}"
                    echo "Jenkins Build: ${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Build') {
            steps {
                sh '''
                    mkdir -p build
                    cp index.html build/
                    cp version.txt build/
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    test -s build/index.html
                    test -s build/version.txt
                    echo "Tests passed"
                '''
            }
        }

        stage('Package Artifact') {
            steps {
                sh '''
                    cd build
                    zip -r "../${ARTIFACT_NAME}" .
                    cd ..

                    sha256sum "${ARTIFACT_NAME}" > "${ARTIFACT_NAME}.sha256"
                    ls -lh "${ARTIFACT_NAME}"*
                '''
            }
        }

        stage('Publish to Nexus') {
            when {
                branch 'feature-login'
            }

            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'nexus-credentials',
                        usernameVariable: 'NEXUS_USER',
                        passwordVariable: 'NEXUS_PASS'
                    )
                ]) {
                    sh '''
                        set +x
                        set -eu

                        ARTIFACT_URL="${NEXUS_URL}/repository/${NEXUS_REPO}/payment-app/${RELEASE_ID}/${ARTIFACT_NAME}"

                        curl --fail --show-error \
                             --user "${NEXUS_USER}:${NEXUS_PASS}" \
                             --upload-file "${ARTIFACT_NAME}" \
                             "${ARTIFACT_URL}"

                        curl --fail --show-error \
                             --user "${NEXUS_USER}:${NEXUS_PASS}" \
                             --upload-file "${ARTIFACT_NAME}.sha256" \
                             "${ARTIFACT_URL}.sha256"

                        echo "Artifact published to Nexus successfully"
                    '''
                }
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts(
                    artifacts: 'payment-app-*.zip*',
                    fingerprint: true
                )
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed - check Console Output'
        }
    }
}
