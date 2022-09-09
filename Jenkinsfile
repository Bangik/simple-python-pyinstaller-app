node {
    docker.image('python:2-alpine').inside {
        stage('Build') {
            sh 'python -m py_compile /var/jenkins_home/workspace/submission-cicd-pipeline-bangik/sources/add2vals.py /var/jenkins_home/workspace/submission-cicd-pipeline-bangik/sources/calc.py' 
            stash includes: 'sources/*.py*', name: 'compiled-results'
        }
        stage('Test') {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    
}