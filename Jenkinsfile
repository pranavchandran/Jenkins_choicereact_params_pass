pipeline {
    agent any

    parameters {
        activeChoice(
            name: 'ENVIRONMENT',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        return ["development", "production"]
                    '''
                ]
            ]
        )
        
        reactiveChoice(
            name: 'BUCKET_NAME',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        if (ENVIRONMENT.equals("development")) {
                            return ["ge-power-cust-wer-source-inventory-test"]
                        } else {
                            return ["ge-power-cust-prod-inventory-test"]
                        }
                    '''
                ]
            ],
            referencedParameters: 'ENVIRONMENT'
        )

        reactiveChoice(
            name: 'SYMLINK_PATH',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        if (ENVIRONMENT.equals("development")) {
                            return ["athena-inventory-report-output/NewInventory/symlink.txt"]
                        } else {
                            return ["athena-inventory-report-output-prod/NewInventory/symlink.txt"]
                        }
                    '''
                ]
            ],
            referencedParameters: 'ENVIRONMENT'
        )

        reactiveChoice(
            name: 'ROLE_ARN',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        if (ENVIRONMENT.equals("development")) {
                            return ["arn:aws:iam::011821064023:role/customer-inventory-report-dev-role"]
                        } else {
                            return ["arn:aws:iam::011821064023:role/customer-inventory-report-prod-role"]
                        }
                    '''
                ]
            ],
            referencedParameters: 'ENVIRONMENT'
        )

        reactiveChoice(
            name: 'CREDENTIALS_ID',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        if (ENVIRONMENT.equals("development")) {
                            return ["AWS_POWER_DEV"]
                        } else {
                            return ["AWS_POWER_PROD"]
                        }
                    '''
                ]
            ],
            referencedParameters: 'ENVIRONMENT'
        )

        reactiveChoice(
            name: 'S3_OUTPUT_LOCATION',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        if (ENVIRONMENT.equals("development")) {
                            return ["s3://ge-power-cust-wer-source-inventory-test/athena-inventory-report-output/Output/"]
                        } else {
                            return ["s3://ge-power-cust-prod-inventory-test/athena-inventory-report-output/Output/"]
                        }
                    '''
                ]
            ],
            referencedParameters: 'ENVIRONMENT'
        )

        reactiveChoice(
            name: 'S3_SYMLINK_LOCATION',
            choiceType: 'PT_SINGLE_SELECT',
            script: [
                $class: 'GroovyScript', 
                script: [
                    script: '''
                        if (ENVIRONMENT.equals("development")) {
                            return ["s3://ge-power-cust-wer-source-inventory-test/athena-inventory-report-output/NewInventory/"]
                        } else {
                            return ["s3://ge-power-cust-prod-inventory-test/athena-inventory-report-output/NewInventory/"]
                        }
                    '''
                ]
            ],
            referencedParameters: 'ENVIRONMENT'
        )
    }

    stages {
        stage('Echo Variables') {
            steps {
                script {
                    echo "ENVIRONMENT: ${params.ENVIRONMENT}"
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
                    bat 'python3 athena_query.py'
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
