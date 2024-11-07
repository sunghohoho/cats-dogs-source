pipeline {
    agent {
        label "kaniko"
    }
    
    environment {
        AWS_REGION = 'ap-northeast-2'  
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
                container("kaniko"){
                    script {
                        dir('web') {
                            echo "Building and pushing webs container."
                            sh """
                                /kaniko/executor --context . --dockerfile ./Dockerfile --destination ${AWS_WEBS_REPO}:${TAG}
                            """
                        }
                    }
                }
            }
        }
        
        stage('Build and Push Cats Container') {
            steps {
                container("kaniko"){
                    script {
                        dir('cats') {
                            echo "Building and pushing cats container.."
                            sh """
                                    /kaniko/executor --context . --dockerfile ./Dockerfile --destination ${AWS_CATS_REPO}:${TAG}
                            """
                        }
                    }
                }
            }
        }

        stage('Build and Push Dogs Container') {
            steps {
                container("kaniko"){
                    script {
                        dir('dogs') {
                            echo "Building and pushing dogs container."
                            sh """
                                    /kaniko/executor --context . --dockerfile ./Dockerfile --destination ${AWS_DOGS_REPO}:${TAG}
                            """
                        }
                    }
                }
            }
        }
    }
}
