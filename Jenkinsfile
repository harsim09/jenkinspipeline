pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: 'http://localhost:8081/', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'http://localhost:8082/', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                bat 'mvn clean install'
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
                        timeout(time:5,unit:'DAYS')
                        {
                        input message: 'Approve'
                        }
                       
                    build job: 'Deploy-To-Prod'
                    }
                    post{
                        success{
                            echo "Success"
                        }
                        failure{
                            echo "Failure"
                        }
                    }  
                        
                }
            }
        }
    }
}
