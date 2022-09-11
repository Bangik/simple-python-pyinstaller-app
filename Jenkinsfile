// node {
//     docker.image('python:2-alpine').inside {
//         stage('Build') {
//             sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
//             stash name: 'compiled-results', includes: 'sources/*.py*'
//         }
//     }
//     docker.image('qnib/pytest').inside{
//         stage('Test') {
//             sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
//             junit 'test-reports/results.xml'
//         }
        
//     }
//     withEnv(["VOLUME=${pwd()}\'/sources:/src\'", 'IMAGE=\'cdrx/pyinstaller-linux:python2\'']) {
//         input 'Lanjutkan ke tahap Deploy?'
//         stage('Deploy') {
//             unstash name: 'compiled-results'
//             sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
//             if (currentBuild.result == null || currentBuild.result == 'SUCCESS'){
//                 sh 'sources/dist/add2vals X YY'
//                 sleep time: 1, unit: 'MINUTES'
//                 archiveArtifacts artifacts: "sources/dist/add2vals", followSymlinks: false
//                 sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
//             }
//         }
//     }
// }

pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
