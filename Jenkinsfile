/* import shared library */
@Library('shared-library')_
pipeline {
     environment {
       IMAGE_NAME = "koolproject"
       IMAGE_TAG = "latest"
       STAGING = "koolmarubo-staging"
       PRODUCTION = "koolmarubo-production"
     }
     agent none
     stages {
         stage('Build image') {
             agent any
             steps {
                script {
                  sh 'echo "Build ok"'
                }
             }
        }
        
       stage('Test image') {
           agent any
           steps {
              script {
                sh '''
                    curl http://localhost | grep -q "Laravel"
                '''
              }
           }
      }
      
     stage('Push image in staging and deploy it') {
       when {
              expression { GIT_BRANCH == 'origin/master' }
            }
      agent any
      environment {
          HEROKU_API_KEY = credentials('heroku_api')
      }  
      steps {
          script {
            sh '''
              heroku container:login
              heroku create $STAGING || echo "project already exist"
              heroku container:push -a $STAGING web
              heroku container:release -a $STAGING web
            '''
          }
        }
     }
     stage('Push image in production and deploy it') {
       when {
              expression { GIT_BRANCH == 'origin/master' }
            }
      agent any
      environment {
          HEROKU_API_KEY = credentials('heroku_api')
      }  
      steps {
          script {
            sh '''
              heroku container:login
              heroku create $PRODUCTION || echo "project already exist"
              heroku container:push -a $PRODUCTION web
              heroku container:release -a $PRODUCTION web
            '''
          }
        }
     }
  }
     post {
          always {
               script {
                    slackNotifier currentBuild.result
               }
          }
     }
}

