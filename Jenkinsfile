pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
            checkout scm
              sh '''
export PATH=$PATH:/usr/local/bin:$HOME/.rbenv/bin:$HOME/.rbenv/shims
eval "$(rbenv init -)"
rbenv local 2.6.0
rbenv rehash
export DISABLE_SPRING=1
bundle install
bin/rails test
'''
sh 'git rev-parse HEAD > commit'
            }
        }
        stage('Deploy') {
            environment {
                GIT_COMMIT = readFile('commit').trim()
            }
            steps {
                script {
             step([$class: 'AWSEBDeploymentBuilder', zeroDowntime: false, 
 awsRegion: 'eu-central-1', applicationName: 'Rails1', environmentName: 'Rails1-env', rootObject: ".",
  versionLabelFormat: env.GIT_COMMIT
 ])
     } 
            }
        }
   }
}

