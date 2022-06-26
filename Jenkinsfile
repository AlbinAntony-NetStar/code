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
        stage ('stash') 
        {
            steps {
                
                sh "echo 1 > myfile.txt" // runs in $WORKSPACE, creates $WORKSPACE/myfile.txt
                stash name: "my_stash", includes: "myfile.txt" // relative to $WORKSPACE
                  }
                    
        }

        stage('Zip the app  directory')
        {
            steps
            {
                sh " zip -r test.zip . "
                sh " /usr/bin/scp -i /var/lib/jenkins/workspace/code-new/aws.pem test.zip ubuntu@13.235.86.211:/home/ubuntu/ "
                sh "rm -rf test.zip "
                sh " echo project runnings "
              
               
            }
        }
        stage('Echo')
        {
            steps
            {
                script
                {
                    withCredentials([string(credentialsId: 'test-id', variable: 'SECRET')]) {
                        echo "My secret text is '${SECRET}'"
}
                }
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
                    remote.host = "13.235.86.211"
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