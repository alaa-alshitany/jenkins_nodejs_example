pipeline {
    agent { 
        label 'private-slave'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'rds_redis', url: 'https://github.com/alaa-alshitany/jenkins_nodejs_example'
            }
        }

        stage('Fetch Secrets') {
            steps {
                script {
                    def secrets = sh(script: '''
                        aws secretsmanager get-secret-value --secret-id rds_credentials --query SecretString --output text
                    ''', returnStdout: true).trim()

                    def json = readJSON text: secrets

                    env.RDS_HOSTNAME = json.host
                    env.RDS_USERNAME = json.username
                    env.RDS_PASSWORD = json.password
                    env.RDS_PORT = json.port
                    env.DB_NAME = json.db_name
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'docker build -f dockerfile -t nodejsapp .'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh '''
                        docker run -d -p 3000:3000 \
                        -e RDS_HOSTNAME \
                        -e RDS_USERNAME \
                        -e RDS_PASSWORD \
                        -e RDS_PORT \
                        -e DB_NAME \
                        --name nodejsapp_container nodejsapp
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deploymrent completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
