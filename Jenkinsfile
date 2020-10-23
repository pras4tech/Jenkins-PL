import groovy.json.JsonSlurper

def getDockerImages() {
    final API_KEY = "FOOBARAPIKEY"
    final REPO_NAME = "service-docker"
    final APP_NAME = "myapp"

    def cmd = [ 'bash', '-c', "curl -H 'X-JFrog-Art-Api: ${API_KEY}' https://artifactory.acme.co/artifactory/api/docker/${REPO_NAME}/v2/${APP_NAME}/tags/list".toString()]
    def result = cmd.execute().text

    def slurper = new JsonSlurper()
    def json = slurper.parseText(result)
    def tags = new ArrayList()
    if (json.tags == null || json.tags.size == 0)
      tags.add("unable to fetch tags for ${APP_NAME}")
    else
      tags.addAll(json.tags)
    return tags.join('\n')
}

pipeline {
    agent any
    stages {
        stage("Gather Deployment Parameters") {
            steps {
                timeout(time: 30, unit: 'SECONDS') {
                    script {
                        // Show the select input modal
                       def INPUT_PARAMS = input message: 'Please Provide Parameters', ok: 'Next',
                                        parameters: [
                                        choice(name: 'ENVIRONMENT', choices: ['dev','qa'].join('\n'), description: 'Please select the Environment'),
                                        choice(name: 'IMAGE_TAG', choices: getDockerImages(), description: 'Available Docker Images')]
                        env.ENVIRONMENT = INPUT_PARAMS.ENVIRONMENT
                        env.IMAGE_TAG = INPUT_PARAMS.IMAGE_TAG
                    }
                }
            }
        }
        stage("Use Deployment Parameters") {
         steps {
                script {
                    echo "All parameters have been set as Environment Variables"
                    echo "Selected Environment: ${env.ENVIRONMENT}"
                    echo "Selected Tag: ${env.IMAGE_TAG}"
                }
         }
        }
    }
}