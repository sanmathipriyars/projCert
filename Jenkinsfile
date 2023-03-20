pipeline {
    agent {label 'node1'}
    stages {
        stage('Configure Deploy Puppet agent') {
            steps {
                echo 'Installing the puppet agent'
                sh '''whoami
		ip=$(hostname -I | awk '{print $1}')
		if [ "$(yum list installed|grep pup)" == "" ];
		then 
		#sudo yum remove puppetlabs-release-22.0-2.noarch -y;fi
		sudo yum -y install http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
		fi
		if [ "$(puppet -V)" == "" ];
		then
		sudo yum -y install puppet;
		fi
		if [ "$(cat /etc/hosts|grep puppet)" == "" ];then
		sudo echo "$ip puppet.example.com  puppet" > /etc/hosts
		fi
		sudo systemctl enable --now puppet
		systemctl status puppet'''
            }
        }
        stage('Install and Configure Docker') {
            steps {
                echo 'Installing Docker using ansible'
                sh '''
		if [ "$(docker --version)" == "" ];
		then
		ansible-playbook ./docker_inst.yml
		fi'''
            }
        }
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/sanmathipriyars/projCert.git'

                // Run docker image
                sh ''' docker build -t sanmathipriya-app-edu .
                docker run --name webapp -d sanmathipriya-app-edu
                cont_id=$(docker ps | grep webapp|awk '{print $1}')
                ip_cont=$(docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(docker ps -aq)|grep webapp|awk '{print $3}')
                curl http://$ip_cont '''
            }
            post {
                failure {
                  sh ''' echo 'Removing container'
                  cont_id=$(docker ps | grep webapp|awk '{print $1}')
                  docker stop $cont_id
		  docker rm $cont_id '''
                }
            }
        }
    }
}
