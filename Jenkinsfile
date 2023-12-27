pipeline {
    agent  {
        label 'jenkins'
    } 
    
    tools {
        maven 'Maven'
    }
    
    stages {
        stage ('Checkout source') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Hari-Ramesh/java-maven-war-appH.git']]) 
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage ('Sonar scanning') { 
            steps {
                withSonarQubeEnv("Sonar") {
                    sh "${tool("Sonar")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://ec2-54-179-59-182.ap-southeast-1.compute.amazonaws.com:9000/ \
                    -Dsonar.login=sqp_047f03ee8671e99ebdac9e49e7cf1f74a6230ebe \
                    -Dsonar.projectKey=java-maven-war-appH \
                    -Dsonar.java.binaries=target "
                }
            }
        }
        stage ('Upload into Nexus') {
            steps {
                sh 'mvn -s settings.xml clean deploy'
            }
        }
        stage ('Deployment') {
            agent {
                label 'Atti'
            }
            steps {
                sh 'ansible-playbook -i inventory.yml deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }
    }
}
