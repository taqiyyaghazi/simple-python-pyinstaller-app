node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } catch (Exception e) {
                echo 'Error: ' + e.toString()
            } finally {
                always {
                    junit 'test-reports/results.xml'
                }
                input message: 'Lanjutkan ke tahap Deploy? (Click "Proceed" to continue)'
            }
        }
    }
    stage('Deploy') {
        docker.image('cdrx/pyinstaller-linux:python2').inside("--entrypoint=''") {
            try {       
                sh 'python --version'
                sh 'wget https://pypi.python.org/packages/source/P/PyInstaller/PyInstaller-3.6.tar.gz'
                sh 'tar -xvzf PyInstaller-3.6.tar.gz'
                sh 'cd PyInstaller-3.6'
                sh 'python setup.py install'
                sh 'pyinstaller --onefile sources/add2vals.py'
            } catch (Exception e) {
                echo 'Error: ' + e.toString()
            } finally {
                archiveArtifacts 'dist/add2vals'
                sleep 60
            }
        }
    }
}