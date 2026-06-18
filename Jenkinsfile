#!groovy

node {

```
def SF_USERNAME = env.SF_USERNAME
def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"
def SERVER_KEY_CREDENTALS_ID = env.SERVER_KEY_CREDENTALS_ID
def SF_CONSUMER_KEY = env.SF_CONSUMER_KEY

println "JWT Credential ID: ${SERVER_KEY_CREDENTALS_ID}"
println "Username: ${SF_USERNAME}"
println "Instance URL: ${SF_INSTANCE_URL}"
println "Consumer Key: ${SF_CONSUMER_KEY}"

def toolbelt = tool 'toolbelt'

stage('Checkout Source') {
    checkout scm
}

withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

    stage('Authorize Dev Hub') {

        def rc

        if (isUnix()) {
            rc = sh(
                returnStatus: true,
                script: """
                ${toolbelt}/sf org login jwt \
                --instance-url ${SF_INSTANCE_URL} \
                --client-id ${SF_CONSUMER_KEY} \
                --username ${SF_USERNAME} \
                --jwt-key-file ${server_key_file} \
                --set-default-dev-hub \
                --alias HubOrg
                """
            )
        } else {
            rc = bat(
                returnStatus: true,
                script: """
                "${toolbelt}\\sf" org login jwt ^
                --instance-url ${SF_INSTANCE_URL} ^
                --client-id ${SF_CONSUMER_KEY} ^
                --username ${SF_USERNAME} ^
                --jwt-key-file "${server_key_file}" ^
                --set-default-dev-hub ^
                --alias HubOrg
                """
            )
        }

        if (rc != 0) {
            error("Salesforce Dev Hub authorization failed.")
        }
    }

    stage('Display Org Info') {

        if (isUnix()) {
            sh "${toolbelt}/sf org display --target-org HubOrg"
        } else {
            bat "\"${toolbelt}\\sf\" org display --target-org HubOrg"
        }
    }

    stage('Deploy Metadata') {

        if (isUnix()) {
            sh """
            ${toolbelt}/sf project deploy start \
            --target-org HubOrg \
            --source-dir force-app
            """
        } else {
            bat """
            "${toolbelt}\\sf" project deploy start ^
            --target-org HubOrg ^
            --source-dir force-app
            """
        }
    }
}
```

}
