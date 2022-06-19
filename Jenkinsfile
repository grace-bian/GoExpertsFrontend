pipeline {
    agent any

    environment {
        BUCKET_NAME = "s3://frontend-static-website-test"
        FRONTEND_PACKAGE = 'frontend'
        BACKEND_PACAGE = 'backend'
    }

    tools {nodejs "nodejs"}

    stages {
        stage('Install packages') {
            steps {
                echo "Installing packages ..."
                //Install the packages from package.json
                // sh 'cd ${FRONTEND_PACKAGE}'
                sh 'npm install --cache="./tmpcache"'
            }
        }
        stage('Build') {
            steps {
                echo "Building ${env.BUILD_ID}"
                // sh 'cd ${FRONTEND_PACKAGE}'
                sh 'npm run build'
            }
        }

        stage('Install AWS CLI') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo "Installing AWS CLI ..."
                sh 'apt-get update'
                sh 'apt install python3-pip -y'
                sh 'pip3 install awscli --upgrade'
            }
        }
        
        stage('Deploy to S3') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Deploying to '
                // Empty the UAT bucket
                sh 'aws s3 rm "${BUCKET_NAME}" --recursive'
                // Copy the static files from workspace to the S3 bucket
                sh 'aws s3 cp "${env.WORKSPACE}/yabber/build" "${BUCKET_NAME}" --recursive'

            }
        }
    }

}

