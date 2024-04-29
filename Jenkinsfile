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
        // stage('Check Out') {
        //     // steps {
        //         // sh 'echo "Checking out helm chart..."'
        //         // git branch: "${BRANCH_NAME}",
        //         // url: "${REPO_URI}"
        //         // sh 'echo "Successfully checked out helm chart"'
        //     }
        // }
        stage('Packaging Helm') {
            steps {
                sh 'echo "Packaging helm chart"'
                sh "helm package ./${APP_NAME} --version 1.0.0 --app-version 1.0.0"
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
