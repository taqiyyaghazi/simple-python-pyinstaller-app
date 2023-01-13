node {
    stage('Build') {
        def python = docker.image('python:2-alpine').inside()
        python.sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    }
    stage('Test') {
        def pytest = docker.image('qnib/pytest').inside()
        pytest.sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        post {
            always {
                junit 'test-reports/results.xml'
            }
        }
    }
    stage('Deploy') {
        def pyinstaller = docker.image('cdrx/pyinstaller-linux:python2').inside()
        pyinstaller.sh 'pyinstaller --onefile sources/add2vals.py'
        post {
            success {
                archiveArtifacts 'dist/add2vals'
            }
        }
    }
}
