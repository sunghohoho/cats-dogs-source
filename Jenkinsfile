pipeline {
    agent {
        label "kaniko"
    }
    
    environment {
        // AWS_ACCESS_ROLE = credentials('AWS_ACCESS_ROLE')
        AWS_REGION = 'ap-northeast-2'  // Jenkins 환경 변수나 Credential 관리에서 설정 가능
        AWS_CATS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-cats'
        AWS_DOGS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-dogs'
        AWS_WEBS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-webs'
    }

    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }
        stage('Set Version Tag') {
            steps {
                script {
                    def date = new Date()
                    date.format('yyyyMMdd.HHmmss', TimeZone.getTimeZone('Asia/Seoul'))
                    TAG = date.format('yyyyMMdd.HHmmss')
                }
            }
        }

        stage('Build and Push Webs Container') {
            steps {
                script {
                    dir('web') {
                        echo "Building and pushing webs container..."
                        sh """
                            // aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin ${AWS_WEBS_REPO}
                            docker build -t ${AWS_WEBS_REPO}:${TAG} .
                            // docker push ${AWS_WEBS_REPO}:${TAG}
                        """
                    }
                }
            }
        }

        // stage('Build and Push Cats Container') {
        //     steps {
        //         script {
        //             dir('cats') {
        //                 echo "Building and pushing cats container..."
        //                 sh """
        //                     // aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin ${AWS_CATS_REPO}
        //                     docker build -t ${AWS_CATS_REPO}:${TAG} .
        //                     // docker push ${AWS_CATS_REPO}:${TAG}
        //                 """
        //             }
        //         }
        //     }
        // }

        // stage('Build and Push Dogs Container') {
        //     steps {
        //         script {
        //             dir('dogs') {
        //                 echo "Building and pushing dogs container..."
        //                 sh """
        //                     // aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin ${AWS_DOGS_REPO}
        //                     docker build -t ${AWS_DOGS_REPO}:${TAG} .
        //                     // docker push ${AWS_DOGS_REPO}:${TAG}
        //                 """
        //             }
        //         }
        //     }
        // }
    }
}
