pipeline { 
     agent {
        label "agentVM1"
    }
    
    triggers {
         pollSCM ('0 * * * *')
    }

    tools {
    nodejs 'NodeJS'
    }

    stages {
        stage("checkout") {
            steps { 
                checkout scm
            }


        }
        stage("Working with NodeJS npm packages") {
            parallel("compressing") {
                stage("clean-css") {
                    steps {
                        nodejs('NodeJS') {
                            sh '''#!/bin/bash -xe
                            cleancss --batch --batch-suffix '.min' www/css/*.css !www/css/*.min.css -o www/min/'''
                        }
                    }
                }
                stage("uglify-js") {
                    steps {
                        nodejs('NodeJS') {
                            sh '''#!/bin/bash -xe
                                files=$(ls -A www/js | sed "s/.js//g")
                                for file in $files; do
                                        uglifyjs  www/js/$file.js -c -o www/min/$file.min.js    
                                done'''
                        }
                    }
                }
            }
        }
        stage("creating tar archive") {
            steps { 
                sh "tar --exclude='.git' --exclude='www/js' --exclude='www/css' -cf result.tar www/"
            }
        } 
        stage("Artifacts") {
            steps {

                archiveArtifacts allowEmptyArchive: true, artifacts: "**/*.tar", fingerprint: true, followSymlinks: false
            }
        }
    }
} 
