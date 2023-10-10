pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "561775821658"
        AWS_DEFAULT_REGION = "ap-south-1"
        IMAGE_REPO_NAME = "skill"
        IMAGE_TAG = "v1"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
        ECS_CLUSTER = "skillgpt"
        ECS_SERVICE = "skillgpt"
        ECS_TASK_DEFINITION = "arn:aws:ecs:${AWS_DEFAULT_REGION}:${AWS_ACCOUNT_ID}:task-definition/task:2"
        IMAGES_TO_DELETE = "UNTAGGED"
    }

    stages {
        stage('Logging into AWS ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${REPOSITORY_URI}"
                }
            }
        }
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[credentialsId: 'your-credentials-id', url: 'https://github.com/rohirborse/sample-nodejs-docker.git']]])
            }
        }

        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps{  
                script {
                    sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
               }
            }
        }
    }   
}
