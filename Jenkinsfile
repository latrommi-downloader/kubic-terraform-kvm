pipeline {
  agent {
    label 'Full-Docker'
  }
  stages {
    stage('TEST') {
      parallel {
        stage('docker --version') {
          steps {
            sh 'docker --version'
          }
        }

        stage('docker ps') {
          steps {
            sh 'docker ps'
          }
        }

        stage('current folder') {
          steps {
            sh 'pwd'
          }
        }

        stage('TF version') {
          steps {
            sh 'terraform version'
          }
        }

      }
    }

    stage('TF Download Images') {
      steps {
        sh '''                               
                ./download-image.py 
                
                '''
      }
    }

    stage('TF Init') {
      steps {
        sh '''                               
                terraform init
                '''
      }
    }

    stage('TF Plan') {
      steps {
        sh '''
terraform plan -out deploy
            '''
      }
    }

    stage('Approval') {
      steps {
        script {
          def userInput = input(id: 'Deploy', message: 'Do you want to deploy?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply terraform', name: 'confirm'] ])
        }

      }
    }

    stage('TF Apply') {
      steps {
        sh '''           
            terraform apply deploy -auto-approve'''
      }
    }

    stage('TF Report') {
      steps {
        sh '''                            
            ./mk-ssh-config.sh
            '''
      }
    }

  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
  }
}