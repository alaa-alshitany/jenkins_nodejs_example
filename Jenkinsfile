pipeline {
    agent { 
        label 'private-slave'
    }
  
    environment {
        RDS_HOSTNAME   = credentials('RDS_HOSTNAME')
        RDS_USERNAME   = credentials('RDS_USERNAME')
        RDS_PASSWORD   = credentials('RDS_PASSWORD')
        RDS_PORT       = credentials('RDS_PORT')
        REDIS_HOSTNAME = credentials('REDIS_HOSTNAME')
        REDIS_PORT     = credentials('REDIS_PORT')
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'rds_redis', url: 'https://github.com/alaa-alshitany/jenkins_nodejs_example'
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
