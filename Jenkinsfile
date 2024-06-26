pipeline {
    agent any

    environment {
        WORKSPACE = "${env.WORKSPACE}"
        JOB_NAME = "${env.JOB_NAME}"
        BASE_PATH = "${WORKSPACE}/${JOB_NAME}"

        REPO_URI = 'https://github.com/Mel-Zedeks/helm-pipeline-test'
        BRANCH_NAME = 'main'

        APP_NAME = 'test-helm-jenkins'
        APP_VERSION = '1.0.0'
        HELM_PACKAGE_NAME = "${APP_NAME}-${APP_VERSION}"
                    //    test-helm-jenkins/1.0.0/test-helm-jenkins-1.0.0.tgz
        HELM_PACKAGE_PATH = "${APP_NAME}/${APP_VERSION}/${HELM_PACKAGE_NAME}.tgz"

        // Nexus Repo
        NEXUS_URL = '172.16.72.131'
        NEXUS_REPO = 'my-helm-repo'
    }

    stages {
        stage('Packaging Helm') {
            steps {
                sh 'echo "Packaging helm chart"'
                sh "helm package . --version ${APP_VERSION} --app-version ${APP_VERSION}"
                sh 'echo "Packaging helm chart completed"'
            }
        }
        stage('Upload to Nexus') {
            steps {
                sh 'echo "Pushing helm package to artifactory."'
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUS_URL}",
                    groupId: '',
                    version: "${APP_VERSION}",
                    repository: "${NEXUS_REPO}",
                    credentialsId: 'nexus-credentials',
                    artifacts: [
                        [
                            artifactId: "${APP_NAME}",
                            classifier: '',
                            file: "${APP_NAME}-${APP_VERSION}.tgz",
                            type: 'tgz'
                        ]
                    ]
                )
                sh 'echo "Successfully pushed helm package."'
            }
        }
        stage('Fetch Helm Package') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                    sh ('curl -u $NEXUS_USERNAME:$NEXUS_PASSWORD $NEXUS_URL/repository/$NEXUS_REPO/$HELM_PACKAGE_PATH -o $HELM_PACKAGE_NAME-k8.tgz ')
                }
            }
        }
    }
    post {
        always {
            emailext body: 'Build ${currentBuild.result}: ${env.BUILD_URL}',
            subject: "Notification - ${currentBuild.result}",
            to: 'melchi.lg@gmail.com'
        }
    }
}