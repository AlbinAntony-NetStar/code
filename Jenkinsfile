pipeline 
{   
    environment
    {        
        APP_NAME = 'code-codeigniter'
    }
    agent any
    stages 
    {
        stage('Cloning the App')
        {
            steps
            {
                checkout scm
                echo "${env.GIT_BRANCH}"
            }
        }
        stage('Echo')
        {
            steps
            {
                script
                {
                    withCredentials([file(credentialsId: '6b446a84-86fa-4ff2-8bb9-fef674a38f71', variable: 'DB')]) {
                    sh 'cat $DB'
                    sh 'cat $DB > app/Config/Database.php' 
                }

                }
            }
        }

        stage('Zip the app  directory')
        {
            steps
            {
                sh " zip -r test.zip . "
                sh " /usr/bin/scp -i /var/lib/jenkins/workspace/code-new/aws.pem test.zip ubuntu@3.109.200.87:/home/ubuntu/ "
                sh "rm -rf test.zip "
                sh " echo project runnings "
              
               
            }
        }
        
    
        stage('Remote login') 
        {
            steps 
            {
                script 
                {
                    def remote = [:]
                    remote.name = "test-app"
                    remote.host = "3.109.200.87"
                    remote.allowAnyHosts = true
                   withCredentials([sshUserPrivateKey(credentialsId: 'test-app', keyFileVariable: 'id', usernameVariable: 'ubuntu')]) 
                    {
                        remote.user = 'ubuntu'
                        remote.password = 'admin'
                        stage("Unzip the Directory") 
                        {  

                            sshCommand remote: remote, command: 'sudo rm -rf /var/www/html/*'

                            sshCommand remote: remote, command: 'sudo unzip /home/ubuntu/test.zip -d /var/www/html/code'
                            
                        }
                        stage("Change the ownership") 
                        {
                            sshCommand remote: remote, command: 'sudo chown www-data. -R /var/www/html/code/'
                        }
                    }
                }
            }
        }    
    }
}