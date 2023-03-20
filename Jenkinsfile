pipeline {
    agent {label 'node1'}
    stages {
        stage('Configure Deploy Puppet agent') {
            steps {
                echo 'Installing the puppet agent'
                sh 'ansible-playbook ./docker_inst.yml'
            }
        }
        stage('Install and Configure Docker') {
            steps {
                echo 'Installing the puppet agent'
                sh 'sudo yum -y install puppet-agent'
                sh 'sudo /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true'
            }
        }
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/sanmathipriyars/projCert.git'

                // Run docker image
                sh "docker build -t sanmathipriya-app-edu .
                docker run --name webapp -d sanmathipriya-app-edu
                cont_id=$(docker ps | grep webapp|awk '{print $1}')
                ip_cont=$(docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(docker ps -aq)|grep webapp|awk '{print $3}')
                curl http://$ip_cont"
            }
            post {
                failure {
                  sh "echo 'Removing container'"
                  cont_id=$(docker ps | grep webapp|awk '{print $1}')
                  docker stop $cont_id
				  docker stop $cont_id
                }
            }
        }
    }
}
