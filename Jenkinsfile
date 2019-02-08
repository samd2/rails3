pipeline {
    agent any
            //environment {
            //    GIT_BRANCH_LOCAL = "staging"
            //}

    stages {
        stage('Test') {
            steps {
            checkout scm
              sh '''
#This command captures GIT_BRANCH without origin appended at the front
echo $GIT_BRANCH | sed -e 's|origin/||g' | tee branch
#This command captures the latest commit
git rev-parse HEAD | tee commit
env
export PATH=$PATH:/usr/local/bin:$HOME/.rbenv/bin:$HOME/.rbenv/shims
eval "$(rbenv init -)"
rbenv local 2.6.0
rbenv rehash
export DISABLE_SPRING=1
bundle install
bin/rails test
'''

sh '''
#This command captures GIT_BRANCH without origin appended at the front
echo $GIT_BRANCH | sed -e 's|origin/||g' | tee branch
#This command captures the latest commit
git rev-parse HEAD | tee commit
#debugging
env
'''

//and now read the value of GIT_BRANCH_LOCAL back in, for later use.
script {
           env.GIT_BRANCH_LOCAL = readFile('branch').trim()
        }

            }
        }
        stage('Deploy-Master') {
            environment {
                GIT_COMMIT = readFile('commit').trim()
                GIT_BRANCH_LOCAL = readFile('branch').trim()
            }
        	when {
    			expression {
        		return env.GIT_BRANCH_LOCAL == 'master';
        		}
    			}
            steps {
             step([$class: 'AWSEBDeploymentBuilder', zeroDowntime: false, 
 awsRegion: 'eu-central-1', applicationName: 'Rails1', environmentName: 'Rails1-env-master', rootObject: ".",
  versionLabelFormat: env.GIT_COMMIT
 ])
            }
        }
       stage('Deploy-staging') {
            environment {
                GIT_COMMIT = readFile('commit').trim()
                GIT_BRANCH_LOCAL = readFile('branch').trim()
            }
                when {
                        expression {
                        return env.GIT_BRANCH_LOCAL == 'staging';
                        }
                        }
            steps {
             step([$class: 'AWSEBDeploymentBuilder', zeroDowntime: false,
 awsRegion: 'eu-central-1', applicationName: 'Rails1', environmentName: 'Rails1-env-staging', rootObject: ".",
  versionLabelFormat: env.GIT_COMMIT
 ])
            }
        }

   }
}

