pipeline {
    agent any

    environment {
        REPO_URI = 'https://github.com/Mel-Zedeks/helm-pipeline-test'
        BRANCH_NAME = 'main'
        APP_NAME = 'test-helm-jenkins'

        // Nexus Repo
        NEXUS_URL = '172.16.72.131'
        NEXUS_REPO = 'my-helm-repo'
    }

    stages {
        stage('Install Helm') {
            steps {
                sh 'echo "Installing helm..."'
                // git branch: "${BRANCH_NAME}",
                // url: "${REPO_URI}"
                script {
                    // Define variables
                    def helmVersion = 'v3.14.0'
                    def helmDownloadUrl = "https://get.helm.sh/helm-${helmVersion}-linux-amd64.tar.gz"

                    // Download and install Helm
                    sh "curl -LO ${helmDownloadUrl}"
                    sh "tar -zxvf helm-${helmVersion}-linux-amd64.tar.gz"
                    // sh 'mv linux-amd64/helm /usr/local/bin/'

                    // Test Helm installation
                    sh './helm version'
                }
                sh 'echo "Successfully installed helm"'
            }
        }
        stage('Packaging Helm') {
            steps {
                sh 'echo "Packaging helm chart"'
                sh './helm package . --version 1.0.0 --app-version 1.0.0'
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
                groupId: 'com.example',
                version: '2.4',
                repository: "${NEXUS_REPO}",
                credentialsId: 'nexus-credentials',
                artifacts: [
                    [
                        artifactId: "${APP_NAME}",
                        classifier: '',
                        file: "${APP_NAME}.tgz",
                        type: 'tgz'
                    ]
                ]
            )
                sh 'echo "Successfully pushed helm package."'
            }
        }
    }
}
