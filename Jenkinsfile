pipeline {
    agent {
        label 'docker'
    }
    options {
        timestamps()
    }
    environment {
        GITHUB_USER_ID = '2b98d5a0-65f8-4961-958d-ad3620541256'
        ALIYUN_USER_ID = '06989ce7-86fb-43ca-aec0-313d260af382'
        IMAGE_TAG = 'registry.cn-shenzhen.aliyuncs.com/tendcode/hao:latest'
    }
    stages {
        stage('Clone sources') {
            options {
                timeout(time: 30, unit: 'SECONDS')
            }
            steps {
                git credentialsId: "${GITHUB_USER_ID}", url: 'https://github.com/Hopetree/hao.git'
            }
        }
        stage('Make vue') {
            steps {
                sh "npm install && npm audit fix && npm run build"
            }
        }
        stage('Build image') {
            steps {
                sh '''pwd && ls -l
                docker build -t ${IMAGE_TAG} --no-cache .
                docker images'''
            }
        }
        stage('Push image') {
            steps {
                withDockerRegistry(credentialsId: "${ALIYUN_USER_ID}", url: 'http://registry.cn-shenzhen.aliyuncs.com') {
                    sh "docker push ${IMAGE_TAG}"
                }
            }
        }
    }
    post {
        always {
            sh "docker images|grep '<none>'|awk '{print \$3}'|xargs docker image rm > /dev/null 2>&1 || true"
            cleanWs()
        }
    }
}