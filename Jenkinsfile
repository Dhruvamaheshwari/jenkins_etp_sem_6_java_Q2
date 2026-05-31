pipeline{
    agent any

    parameters{

        string(
            name: "Git_Branch",
            defaultValue: "main",
            description: "Enter The Git Branch Name"
        )
    }

    tools{
        maven "Maven-3.9"
        jdk "JDK-23"
    }

    environment{
        DOCKER_IMAGE = "dhruvamaheshwari47/etp_Q2"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "ept_Q2"
        PORT = "8080"
    }

    stages{
        stage("clone")
        {
            steps{
                git url: "https://github.com/Dhruvamaheshwari/jenkins_etp_sem_6_java_Q2.git",
                branch: "${params.Git_Branch}"
            }
        }
        stage("Install the dependency")
        {
            steps{
                bat "mvn clean install"
            }
        }
        stage("unit test")
        {
            steps{
                bat "mvn test"
            }
        }
        stage("code quality")
        {
            steps{
                bat "mvn verify"
            }
        }
        stage("build the docker image")
        {
            steps{
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        stage("login and push the image on docker hub")
        {
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId:"dockerhub",
                        usernameVariable:"DOCKER_NAME",
                        passwordVariable:"DOCKER_PASSWORD"
                    )
                ]){
                    bat """
                        echo %DOCKER_PASSWORD%| docker login -u %DOCKER_NAME% --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }
        stage("stop the old Container")
        {
            steps{
                bat "docker rm -f ${CONTAINER_NAME}|| true"
            }
        }
        stage("Re_Run the container")
        {
            steps{
                bat "docker run -d -p ${PORT}:8080 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
    post{
        success{
            echo "Pipeline run successfully."
        }
    }
}