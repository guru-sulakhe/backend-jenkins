pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES') 
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment{
        def appVersion = '' // global variable which can be accessed anywhere within the file
        nexusUrl = 'nexus.guru97s.cloud:8081'
    }
    stages {
        stage('Read The Version'){
            steps {
                script{
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "application version: $appVersion"
                }
            }
        }
        stage('Installing Dependencies') {
            steps {
                sh """
                npm install
                ls -ltr
                echo "application version: $appVersion"
                """
            }
        }
        stage('Build'){ // ziping dependencies and version of the backend
            steps { 
                sh """
                zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                ls -ltr
                """
            }
        }
        stage('Sonar Scan') {
            environment {
                scannerHome = tool 'sonar-6.0' // refering scanner CLI
            }
            steps {
                script {
                    withSonarQubeEnv('sonar-6.0') { // refering sonar server 
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        // stage("Quality Gate") {
        //     steps {
        //       timeout(time: 30, unit: 'MINUTES') {
        //         waitForQualityGate abortPipeline: true
        //       }
        //     }
        //   }
        stage('Nexus Artifact Uploader'){ // uploading the backend zip to the nexus repository(backend)
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "backend",
                            classifier: '',
                            file: "backend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
                     )

                }
            }
        }
        stage('Deploy'){ //transfering build job backend to backend-deploy and passing appVersion as input to the backend-deploy(pipeline)
            when {
                expression {
                    params.deploy
                }
            }
            steps {
                script {
                    def params = [
                    string(name: 'appVersion', value: "${appVersion}")
                ]
                    build job: 'backend-deploy', parameters: params, wait: false  // when we include wait:false upstream job won't wait for downstream job
                     
                }
            }
        }
    }
        post { 
        always { 
            echo 'I will always say Hello again!'
            //deleteDir()
        }
        success { 
            echo 'I will run only when pipeline is success'
        }
        failure { 
            echo 'I will run only when pipeline is failure'
        }
      }
    }