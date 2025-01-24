pipeline {
    agent { 
        label 'maven'  // Use your Maven server as the agent
    }

    environment {
        GITHUB_TOKEN = credentials('github-token-id')
        NEXUS_PROD_CRED = credentials('nexus-prod') // Jenkins credentials ID for Nexus Production
        NEXUS_STAG_CRED = credentials('nexus-stag') // Jenkins credentials ID for Nexus Staging
    }

    stages {
        stage('Git Checkout') {
            steps {
                // Checkout code from the Git repository
                git url: 'https://github.com/vickeyys/jenkins-java-project-1.git', credentialsId: 'github-token-id'
            }
        }

        stage('Compile') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Compiling for Production branch"
                        sh 'mvn clean compile -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Compiling for Staging branch"
                        sh 'mvn clean compile -P stag'
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not valid for this pipeline"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Running tests for Production"
                        sh 'mvn test -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Running tests for Staging"
                        sh 'mvn test -P stag'
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not valid for this pipeline"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Building for Production"
                        sh 'mvn package -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Building for Staging"
                        sh 'mvn package -P stag'
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not valid for this pipeline"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Deploying to Production Nexus Repository"
                        sh """
                            mvn deploy -P prod \
                            -DaltDeploymentRepository=nexus-prod::default::http://54.145.71.222:8081/repository/production/ \
                            -Dnexus.username=${NEXUS_PROD_CRED_USR} \
                            -Dnexus.password=${NEXUS_PROD_CRED_PSW}
                        """
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Deploying to Staging Nexus Repository"
                        sh """
                            mvn deploy -P stag \
                            -DaltDeploymentRepository=nexus-stag::default::http://54.145.71.222:8081/repository/stag/ \
                            -Dnexus.username=${NEXUS_STAG_CRED_USR} \
                            -Dnexus.password=${NEXUS_STAG_CRED_PSW}
                        """
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not valid for this pipeline"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build and deployment completed successfully!"
        }
        failure {
            echo "Build or deployment failed. Check the logs for details."
        }
    }
}
