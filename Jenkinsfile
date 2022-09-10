node {
    docker.image('python:2-alpine').inside {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
            stash name: 'compiled-results', includes: 'sources/*.py*'
        }
    }
    docker.image('qnib/pytest').inside{
        stage('Test') {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
        
    }
    withEnv(["VOLUME=${pwd()}\'/sources:/src\'", 'IMAGE=\'cdrx/pyinstaller-linux:python2\'']) {
        // input 'Lanjutkan ke tahap Deploy?'
        stage('Deploy') {
            unstash name: 'compiled-results'
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
            if (currentBuild.result == null || currentBuild.result == 'SUCCESS'){
                sh 'sources/dist/add2vals X Y'
                // sleep time: 1, unit: 'MINUTES'
                archiveArtifacts artifacts: "sources/dist/add2vals", followSymlinks: false
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
            }
            
        }
        stage('Deploy to heroku') {
            withCredentials([usernamePassword(credentialsId: '9564d061-f057-4011-9abb-87affcda124a', usernameVariable: 'herokuapi', passwordVariable: '196e6f29-f79f-44a1-b085-0f4edd8a8028')]) {
                sh "sudo curl https://cli-assets.heroku.com/install-ubuntu.sh | sh"
                sh "ls"
            }
        }
    }
}