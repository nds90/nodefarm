pipeline{
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
        TOKEN_GITHUB = credentials('githubToken')
        PASS_DOCKERHUB = credentials('dockerhub')
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t nds90/nodefarm-image:${DOCKER_TAG}"
            }
        }
        
        stage('Push Image DockerHub'){
            steps{
                sh ('echo $PASS_DOCKERHUB | docker login -u nds90 --password-stdin')
                sh "docker push nds90/nodefarm-image:${DOCKER_TAG}"
            }
        }

        stage('Clone Deployment Manifest'){
            steps{
                  git branch: 'main', credentialsId: 'github-login', url: 'https://github.com/nds90/deployment-nodefarm.git'
                  sh "chmod +x changeTag.sh"
                  sh "./changeTag.sh ${DOCKER_TAG}"
                  sh "git add ."
                  sh "git config --global user.name 'nds90'"
                  sh "git config --global user.email 'niko.syarbaini@sigma.co.id'"
                  sh "git commit -m 'Image Tag Version ${DOCKER_TAG}'"
            }
        }

        stage('Push to Repo deployment-nodefarm'){
            steps{
                  sh ('git push https://$TOKEN_GITHUB@github.com/nds90/deployment-nodefarm.git')
            }
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}

 //               git branch: 'master', credentialsId: 'login-gitlab-ndsmy', url: 'https://gitlab.nds.my.id/gitops/gitops.git'
 //               sh "chmod +x changeTag.sh"
 //               sh "./changeTag.sh ${DOCKER_TAG}"
 //               sh "git add ."
 //               sh "git config --global user.name 'nds'"
 //               sh "git config --global user.email 'niko.syarbaini@sigma.co.id'"
 //               sh "git commit -am 'Publish new version'"
 //       stage('Push Deploy To Gitlab'){
 //           steps{
 //               withCredentials([usernamePassword(credentialsId: 'login-gitlab-ndsmy', passwordVariable: 'password', usernameVariable: 'username')]) {
 //               sh "git config --global push.default matching"
 //               sh('git push https://${username}:${password}@gitlab.nds.my.id/gitops/gitops.git')
 //               }
 //           }
  //      }