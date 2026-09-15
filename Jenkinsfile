pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bhaskarh993-oss/python-alb-app.git'
            }
        }

        stage('Deploy to Ubuntu') {
            steps {
                sshagent(['ec2-deploy-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.248 "
                            rm -rf ~/python-app &&
                            mkdir -p ~/python-app
                        "

                        scp -o StrictHostKeyChecking=no \
                            app.py requirements.txt \
                            ubuntu@10.0.2.248:~/python-app/

                        ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.248 "
                            cd ~/python-app &&
                            python3 -m venv venv &&
                            ./venv/bin/pip install -r requirements.txt &&
                            pkill -f 'app.py' || true &&
                            nohup ./venv/bin/python app.py > app.log 2>&1 &
                        "
                    '''
                }
            }
        }

        stage('Deploy to Amazon Linux') {
            steps {
                sshagent(['ec2-deploy-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ec2-user@10.0.6.29 "
                            rm -rf ~/python-app &&
                            mkdir -p ~/python-app
                        "

                        scp -o StrictHostKeyChecking=no \
                            app.py requirements.txt \
                            ec2-user@10.0.6.29:~/python-app/

                        ssh -o StrictHostKeyChecking=no ec2-user@10.0.6.29 "
                            cd ~/python-app &&
                            python3 -m venv venv &&
                            ./venv/bin/pip install -r requirements.txt &&
                            pkill -f 'app.py' || true &&
                            nohup ./venv/bin/python app.py > app.log 2>&1 &
                        "
                    '''
                }
            }
        }
    }
}
