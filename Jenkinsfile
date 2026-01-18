pipeline {
    agent any
    tools {
        jdk 'Java21'
    }
    stages {
        stage('Get Code') {
            steps {
                git 'https://github.com/apastorsales/helloworld'
            }
        }
        stage('Build') {
            steps {
                echo 'Python no se compila >:C'
                echo WORKSPACE
                bat 'dir'
            }
        }
        stage('Tests') {
            parallel {
                stage('Unit') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                            bat '''
                                set PYTHONPATH=%WORKSPACE%
                                python -m pip install pytest
                                python -m pytest --junitxml=result-unit.xml test\\unit
                            '''
                        }
                        
                    }
                }
                stage('Service') {
                    steps {
                        bat '''
                            python -m pip install flask
                            set FLASK_APP=app\\api.py
                            start flask run

                            start java -jar C:\\Users\\alfas\\Documents\\Jenkins\\helloworld-master\\wiremock\\wiremock-standalone-3.13.2.jar --port 9090 --root-dir test\\wiremock
                            set PYTHONPATH=%WORKSPACE%
                            python -m pytest --junitxml=result-rest.xml test\\rest
                        '''
                    }
                }
            }
        }
        stage('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}