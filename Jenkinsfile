pipeline {
    agent any
    properties([
        parameters([
            activeChoicesReactiveParam(
                name: 'TEST_PARAM',
                description: 'A simple reactive parameter',
                choiceType: 'SINGLE_SELECT',
                groovyScript: [
                    classpath: [],
                    sandbox: true,
                    script: '''
                        return ["Option1", "Option2", "Option3"]
                    '''
                ]
            )
        ])
    ])
    stages {
        stage('Test') {
            steps {
                script {
                    echo "Selected option: ${params.TEST_PARAM}"
                }
            }
        }
    }
}
