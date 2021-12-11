#!/usr/bin/env groovy
@Library('piper-lib-os') _
pipeline {
    agent any
    stages {
        stage('Build & Deploy') {
            environment{
                GIT_TOKEN = credentials('token-mohith')   
            }
            steps {
		dockerExecute(script: this, dockerImage:'cirrusci/flutter:latest' ){	
            dir("noteit") {
                sh "flutter clean"
                sh "flutter build web"
            } 
        }
        sh "rm -fr .git"
        sh "rm -rf deploy"
        sh "mkdir deploy"
        dir("deploy") {
            sh "git clone https://github.com/Mohithraj916/note-it.git"
        }
        sh "cp -r noteit/build/web/* deploy/note-it/"
        dir("deploy") {
            dir("note-it") {
                sh "git remote rm origin && git remote add origin https://${GIT_TOKEN}@github.com/Mohithraj916/note-it.git"
                sh "git config user.email 'mohithrajkulal916@gmail.com'"
                sh "git config user.name 'Mohithraj Kulal'"
                sh "git add . && git commit -m 'New Build: ${currentBuild.fullDisplayName}'"
                sh "git push -u origin master"
            }
        }

    }
} }
    post{
       success {
            echo 'Job succeeeded!'
            mail to: 'mohithraj.b.j@sap.com',
             subject: "NOTE IT Build Completed Pipeline: ${currentBuild.fullDisplayName}",
             body: "All tests are fine"
        }
        failure {
            echo 'Job failed :('
            mail to: 'mohithraj.b.j@sap.com',
             subject: "NOTE IT Build Failed Pipeline: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL}"
        }
      always {
	    cleanWs(cleanWhenNotBuilt: false,
		    deleteDirs: true,
		    disableDeferredWipeout: true,
		    notFailBuild: true,
		    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
			       [pattern: '.propsfile', type: 'EXCLUDE']])
	}
    }
}