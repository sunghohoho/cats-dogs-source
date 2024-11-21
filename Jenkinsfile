pipeline {
    agent {
        label "kaniko"
    }
    
    environment {
        AWS_REGION = 'ap-northeast-2'  
        AWS_CATS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-cats'
        AWS_DOGS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-dogs'
        AWS_WEBS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-webs'
        HELM_REPO = 'https://github.com/sunghohoho/cats-and-dogs-helm'
        TAG = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
    }

    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage('Build and Push Webs Container') {
            steps {
                container("kaniko"){
                    script {
                        dir('web') {
                            echo "Building and pushing webs container.."
                            sh """
                                /kaniko/executor --context . --dockerfile ./Dockerfile --destination ${AWS_WEBS_REPO}:${TAG}
                            """
                        }
                    }
                }
            }
        }
        
        // stage('Build and Push Cats Container') {
        //     steps {
        //         container("kaniko"){
        //             script {
        //                 dir('cats') {
        //                     echo "Building and pushing cats container..."
        //                     sh """
        //                             /kaniko/executor --context . --dockerfile ./Dockerfile --destination ${AWS_CATS_REPO}:${TAG}
        //                     """
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage('Build and Push Dogs Container') {
        //     steps {
        //         container("kaniko"){
        //             script {
        //                 dir('dogs') {
        //                     echo "Building and pushing dogs container.."
        //                     sh """
        //                             /kaniko/executor --context . --dockerfile ./Dockerfile --destination ${AWS_DOGS_REPO}:${TAG}
        //                     """
        //                 }
        //             }
        //         }
        //     }
        // }
        
        stage('Update dev-values.yaml') {
            steps {
                git $HELM_REPO
                
                script {
                    // dev-values.yaml 파일에서 tag 값 업데이트
                    sh """
                    sed -i "s|tag: .*|tag: ${env.TAG}|" dev-values.yaml
                    git config user.name "jenkins"
                    git config user.email "jenkins@example.com"
                    git add dev-values.yaml
                    git commit -m "Update tag in dev-values.yaml to ${env.TAG}"
                    git push
                    """
                }
            }
        }
    }
}
