pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: 'http://localhost:8092/', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'http://localhost:9090/', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                bat 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        build job: 'Deploy-To-Staging'
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                       build job: 'deploy-to-prod'
                    }
                }
            }
        }
    }
}
