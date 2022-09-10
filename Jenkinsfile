node {
    docker.image('python:2-alpine').inside {
        stage('Build') {
            // sh 'ls'
            // echo pwd()
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
        stage('Deploy') {
            unstash name: 'compiled-results'
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
            if (currentBuild.result == null || currentBuild.result == 'SUCCESS'){
                archiveArtifacts artifacts: "sources/dist/add2vals", followSymlinks: false
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
            }
        }
    }
}