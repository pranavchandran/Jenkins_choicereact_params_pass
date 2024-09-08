pipeline {
    agent any

    // Use the parameters block outside of options
    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['development', 'production'],
            description: 'Select the environment'
        )
        reactiveChoice(
            name: 'BUCKET_NAME',
            description: 'Select the S3 bucket name based on environment',
            choiceType: 'SINGLE_SELECT',
            groovyScript: [
                classpath: [],
                sandbox: true,
                script: '''
                    if (ENVIRONMENT == "development") {
                        return ["ge-power-cust-wer-source-inventory-test"]
                    } else if (ENVIRONMENT == "production") {
                        return ["ge-power-cust-prod-inventory-test"]
                    } else {
                        return []
                    }
                '''
            ]
        )
        reactiveChoice(
            name: 'SYMLINK_PATH',
            description: 'Select the symlink path based on environment',
            choiceType: 'SINGLE_SELECT',
            groovyScript: [
                classpath: [],
                sandbox: true,
                script: '''
                    if (ENVIRONMENT == "development") {
                        return ["athena-inventory-report-output/NewInventory/symlink.txt"]
                    } else if (ENVIRONMENT == "production") {
                        return ["athena-inventory-report-output-prod/NewInventory/symlink.txt"]
                    } else {
                        return []
                    }
                '''
            ]
        )
        reactiveChoice(
            name: 'ROLE_ARN',
            description: 'Role ARN for AWS based on environment',
            choiceType: 'SINGLE_SELECT',
            groovyScript: [
                classpath: [],
                sandbox: true,
                script: '''
                    if (ENVIRONMENT == "development") {
                        return ["arn:aws:iam::011821064023:role/customer-inventory-report-dev-role"]
                    } else if (ENVIRONMENT == "production") {
                        return ["arn:aws:iam::011821064023:role/customer-inventory-report-prod-role"]
                    } else {
                        return []
                    }
                '''
            ]
        )
        reactiveChoice(
            name: 'CREDENTIALS_ID',
            description: 'AWS Credentials ID based on environment',
            choiceType: 'SINGLE_SELECT',
            groovyScript: [
                classpath: [],
                sandbox: true,
                script: '''
                    if (ENVIRONMENT == "development") {
                        return ["AWS_POWER_DEV"]
                    } else if (ENVIRONMENT == "production") {
                        return ["AWS_POWER_PROD"]
                    } else {
                        return []
                    }
                '''
            ]
        )
        reactiveChoice(
            name: 'S3_OUTPUT_LOCATION',
            description: 'S3 Output Location based on environment',
            choiceType: 'SINGLE_SELECT',
            groovyScript: [
                classpath: [],
                sandbox: true,
                script: '''
                    if (ENVIRONMENT == "development") {
                        return ["s3://ge-power-cust-wer-source-inventory-test/athena-inventory-report-output/Output/"]
                    } else if (ENVIRONMENT == "production") {
                        return ["s3://ge-power-cust-prod-inventory-test/athena-inventory-report-output/Output/"]
                    } else {
                        return []
                    }
                '''
            ]
        )
        reactiveChoice(
            name: 'S3_SYMLINK_LOCATION',
            description: 'S3 Symlink Location based on environment',
            choiceType: 'SINGLE_SELECT',
            groovyScript: [
                classpath: [],
                sandbox: true,
                script: '''
                    if (ENVIRONMENT == "development") {
                        return ["s3://ge-power-cust-wer-source-inventory-test/athena-inventory-report-output/NewInventory/"]
                    } else if (ENVIRONMENT == "production") {
                        return ["s3://ge-power-cust-prod-inventory-test/athena-inventory-report-output/NewInventory/"]
                    } else {
                        return []
                    }
                '''
            ]
        )
    }

    stages {
        stage('Echo Variables') {
            steps {
                script {
                    echo "BUCKET_NAME: ${params.BUCKET_NAME}"
                    echo "SYMLINK_PATH: ${params.SYMLINK_PATH}"
                    echo "ROLE_ARN: ${params.ROLE_ARN}"
                    echo "CREDENTIALS_ID: ${params.CREDENTIALS_ID}"
                    echo "S3_OUTPUT_LOCATION: ${params.S3_OUTPUT_LOCATION}"
                    echo "S3_SYMLINK_LOCATION: ${params.S3_SYMLINK_LOCATION}"
                }
            }
        }

        stage('Checkout') {
            steps {
                script {
                    scmVars = checkout scm
                    BRANCH_NAME = scmVars.GIT_BRANCH.tokenize('/')[-1]
                    echo "Branch name: ${BRANCH_NAME}"
                }
            }
        }

        stage('Run Python Code') {
            steps {
                script {
                    sh 'python3 athena_query.py'
                }
            }
        }

        stage('Completed') {
            steps {
                script {
                    echo "Pipeline completed successfully."
                }
            }
        }
    }
}