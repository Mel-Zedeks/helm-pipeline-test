pipeline {
    agent any

    environment {
        //  REPO_URI = "https://github.com/Zedeks-Digital/edupah-go"
        //  BRANCH_NAME = "jenkins"
         APP_NAME = 'est-helm-jenkins'

         // Nexus Repo
         NEXUS_URL = '172.16.72.131'
         NEXUS_REPO = 'my-helm-repo'
    }

    stages {
        stage('Check Out') {
            steps {
                sh 'echo "Checking out helm chart..."'
                sh "helm package ./${APP_NAME} --version 1.0.0 --app-version 1.0.0"
                sh 'echo "Successfully checked out helm chart"'
            }
        }
         stage('Upload to Nexus') {{
            step {
                 sh 'echo "Pushing helm package to artifactory."'
                nexusArtifactUploader {
                    nexusVersion('nexus3')
                    protocol('http')
                    nexusUrl("${NEXUS_URL}")
                    groupId('com.example')
                    version('2.4')
                    repository("${NEXUS_REPO}")
                    credentialsId('nexus-credentials')
                    artifact {
                        artifactId("${APP_NAME}")
                        type('tgz')
                        file("${APP_NAME}")
                    }
                }
                sh 'echo "Successfully pushed helm package."'
            }
         }
    }
}
