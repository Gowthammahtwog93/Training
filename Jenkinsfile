pipeline{
    agent any

    tools {
        maven 'maven_3.9.3'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Gowthammahtwog93/Training.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('SonarQube'){
            steps{
                withSonarQubeEnv("SonarQube") {
                    sh "${tool("Sonar_Scan_5.0.0")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://13.127.93.230:9000/ \
                    -Dsonar.login=sqp_0a056f229e169e6b87f7c6a377dc3599ef8bbc64 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven-war-app"                    
                }
            }
        }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
