pipeline {
    agent any
    tools {
        //Necesario en mi caso debido a la previa existencia de un JRE incompatible con la realizacion de la practica
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
                                pytest --junitxml=result-unit.xml test\\unit
                            '''
                        }
                        
                    }
                }
                stage('Service') {
                    steps {
                        //'Sleep trick' by https://serverfault.com/questions/799879/how-can-i-sleep-in-a-windows-batch-file-within-a-non-interactive-session
                        bat '''
                            set FLASK_APP=app\\api.py                          
                            start flask run

                            ping -n 4 127.0.0.1

                            start java -jar C:\\Jenkins\\wiremock\\wiremock-standalone-3.13.2.jar --port 9090 --root-dir C:\\Jenkins\\wiremock
                            set PYTHONPATH=%WORKSPACE%

                            ping -n 15 127.0.0.1
                            
                            pytest --junitxml=result-rest.xml test\\rest
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