pipeline {
    agent {
        label "kaniko"
    }
    
    environment {
        AWS_REGION = 'ap-northeast-2'  
        AWS_CATS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-cats'
        AWS_DOGS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-dogs'
        AWS_WEBS_REPO = '866477832211.dkr.ecr.ap-northeast-2.amazonaws.com/abc-webs'
        HELM_VALUES_REPO = 'https://github.com/sunghohoho/cad-helm-values.git'
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
                withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GITHUB_USER', passwordVariable: 'GITHUB_TOKEN')]) {
                    script {

                        // Checkout the Helm values repository with authentication using the GitHub token
                        dir('cad-helm-values') {
                            echo ${TAG}
                            echo "Cloning the repository ${HELM_VALUES_REPO}"
                            sh """
                            git config --global credential.helper 'store'
                            git clone https://username:${GITHUB_TOKEN}@github.com/sunghohoho/cad-helm-values.git
                            cd cad-helm-values
                            sed -i "s|tag: .*|tag: $(echo ${TAG} | sed 's/[&/\|]/\\&/g')|" dev-values.yaml
                            git config user.name "jenkins"
                            git config user.email "jenkins@example.com"
                            git add dev-values.yaml
                            git commit -m "Update tag to ${env.TAG}"
                            git push origin main
                            """
                        }
                    }
                }
            }
        }
    }
}
