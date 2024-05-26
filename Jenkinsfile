pipeline {
    agent { 
        label 'private-slave'
    }
  
    environment {
        RDS_HOSTNAME   = sh(script: 'aws ssm get-parameter --name RDS_HOSTNAME --query Parameter.Value --output text', returnStdout: true).trim()
        RDS_USERNAME   = sh(script: 'aws ssm get-parameter --name RDS_USERNAME --query Parameter.Value --output text', returnStdout: true).trim()
        RDS_PASSWORD   = sh(script: 'aws ssm get-parameter --with-decryption --name RDS_PASSWORD --query Parameter.Value --output text', returnStdout: true).trim()
        RDS_PORT       = sh(script: 'aws ssm get-parameter --name RDS_PORT --query Parameter.Value --output text', returnStdout: true).trim()
        REDIS_HOSTNAME = sh(script: 'aws elasticache describe-cache-clusters --cache-cluster-id cluster --show-cache-node-info | jq -r \'.CacheClusters[0].CacheNodes[0].Endpoint.Address\'', returnStdout: true).trim()
        REDIS_PORT     = sh(script: 'aws elasticache describe-cache-clusters --cache-cluster-id cluster --show-cache-node-info | jq -r \'.CacheClusters[0].CacheNodes[0].Endpoint.Port\'', returnStdout: true).trim()
    
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
            echo 'Deployment completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
