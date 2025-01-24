pipeline {
    agent { 
        label 'maven' // Use your Maven server as the agent
    }

    environment {
        GITHUB_TOKEN = credentials('github-token-id') // Replace with your GitHub token credentials ID
        NEXUS_PROD_CRED = credentials('nexus-prod')  // Replace with your Nexus production credentials ID
        NEXUS_STAG_CRED = credentials('nexus-stag')  // Replace with your Nexus staging credentials ID
    }

    stages {
        stage('Git Checkout') {
            steps {
                // Checkout the correct branch
                git url: 'https://github.com/vickeyys/jenkins-java-project-1.git', credentialsId: 'github-token-id'
            }
        }

        stage('Compile') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Compiling for production"
                        sh 'mvn clean compile -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Compiling for staging"
                        sh 'mvn clean compile -P stag'
                    } else {
                        error "Branch ${env.BRANCH_NAME} is not configured for this pipeline."
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Running tests for production"
                        sh 'mvn test -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Running tests for staging"
                        sh 'mvn test -P stag'
                    }
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Packaging for production"
                        sh 'mvn package -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Packaging for staging"
                        sh 'mvn package -P stag'
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
                            -DaltDeploymentRepository=nexus-prod::default::http://54.145.71.222:8081/repository/nexus-prod/ \
                            -Dnexus.username=${NEXUS_PROD_CRED_USR} \
                            -Dnexus.password=${NEXUS_PROD_CRED_PSW} -X
                        """
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Deploying to Staging Nexus Repository"
                        sh """
                            mvn deploy -P stag \
                            -DaltDeploymentRepository=nexus-stag::default::http://54.145.71.222:8081/repository/nexus-stag/ \
                            -Dnexus.username=${NEXUS_STAG_CRED_USR} \
                            -Dnexus.password=${NEXUS_STAG_CRED_PSW} -X
                        """
                    } else {
                        error "Branch ${env.BRANCH_NAME} is not configured for deployment."
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs for more details."
        }
    }
}
