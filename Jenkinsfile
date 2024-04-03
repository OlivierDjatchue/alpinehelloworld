pipeline{
    environment {
        INAGE_NAME ="alpinehelloworld"
        INAGE_TAG ="latest"
        STAGING = "olivierbana-staging"
        PRODUCTION = "olivierbana-prod"
    }
    agent none
    stages{
        stage('Build image'){
            agent any
            steps{
                script {
                    sh 'docker build -t olivierdja/$INAGE_NAME:$INAGE_NAME'
                }
            }
        }
        stage('Run container'){
            agent any
            steps{
                script {
                    sh '''
                    docker run --name=$INAGE_NAME -dp 83:5000 -e PORT=5000 olivierdja/$INAGE_NAME:$INAGE_NAME
                    sleep 5
                    
                    '''
                }
            }
        }

        stage('Test image'){
            agent any
            steps{
                script {
                    sh '''
                    curl http://54.90.227.61:83 | grep "Olivier Djatchue"
                    sleep 5
                    
                    '''
                }
            }
        }
        stage('Clean container'){
            agent any
            steps{
                script {
                    sh '''
                    docker stop $IMAGE_NAME
                    docker rm $IMAGE_NAME
                    '''
                }
            }
        }
        stage('push imahe in staging and deploy'){
            when{
                expression {GIT_BRANCH==origin/master}
            }
            agent any
            environment{
                HEROKU_API_KEY = credentiales('heroku_api_key')
            }
            steps{
                script {
                    sh '''
                    heroku container:login
                    heroku create $STAGING || echo "Project already exist"
                    heroku container:push -a $STAGING web
                    heroku container:release -a $STAGING web
                    
                    '''
                }
            }
        }
        stage('push imahe in PRODUCTION and deploy'){
            when{
                expression {GIT_BRANCH==origin/master}
            }
            agent any
            environment{
                HEROKU_API_KEY = credentiales('heroku_api_key')
            }
            steps{
                script {
                    sh '''
                    heroku container:login
                    heroku create $PRODUCTION || echo "Project already exist"
                    heroku container:push -a $PRODUCTION web
                    heroku container:release -a $PRODUCTION web
                    
                    '''
                }
            }
        }
    }
}
