pipeline{
    agent any
    tools {
        maven 'M3'
        jdk 'java'
    }
    environment {
        imagenamedemo = "registry.hub.docker.com/anishkumar7836/demo"
        imagenamedemo2 = "registry.hub.docker.com/anishkumar7836/demo2"
        dockerImage = ''
    }
    stages{
        stage("Git Clone Repository"){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'anish-github-repo', url: 'https://github.com/anishkumar7836/demo.git']]])
            }
        }
        stage("List Project Directory"){
            steps{
            sh 'ls -l && ls -l demo && ls -l demo2'
            }
        }
        stage("Build Demo Project and Create Docker Image"){
            steps{
                sh 'cd demo && mvn clean package docker:build'
            }
        }
        stage("Build Demo2 Project and Create Docker Image"){
            steps{
                sh 'cd demo2 && mvn clean package docker:build'
            }
        }
        stage("list Docker images demo app"){
            steps{
                sh 'docker images demo'
            }
        }
        stage("list Docker images demo2 app"){
            steps{
                sh 'docker images demo2'
            }
        }
        stage("Tagging image demo app"){
            steps{
            sh " docker tag demo:latest $imagenamedemo:$BUILD_NUMBER"
            }
        }
        stage("Push demo image to Registry"){
            steps{
                withDockerRegistry(credentialsId: 'docker-login', url: 'https://registry.hub.docker.com'){
                sh " docker push $imagenamedemo:$BUILD_NUMBER"
                }
            }
        }
        stage("Tagging image demo2 app"){
            steps{
            sh " docker tag demo2:latest $imagenamedemo2:$BUILD_NUMBER"
            }
        }
        
        stage("Push demo2 image to Registry"){
            steps{
                withDockerRegistry(credentialsId: 'docker-login', url: 'https://registry.hub.docker.com'){
                sh " docker push $imagenamedemo2:$BUILD_NUMBER"
                }
            }
        }
         stage("Delete Unused docker image"){
            steps{
            sh "docker rmi $imagenamedemo2:$BUILD_NUMBER"
            sh "docker rmi $imagenamedemo:$BUILD_NUMBER"
            sh "docker rmi demo2:latest"
            sh "docker rmi demo:latest"
            sh "docker rmi demo2:0.0.1-SNAPSHOT"
            sh "docker rmi demo:0.0.1-SNAPSHOT"
            }
        }
         stage("Trigger ManifestUpdate"){
            steps{
                sh 'echo "triggering updatemanifestjob"'
                build job: 'demo-deploy', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
                }
            }
    }
}
