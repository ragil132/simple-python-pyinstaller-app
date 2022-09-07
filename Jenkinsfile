node {
    stage('Build'){
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    stage('Test'){
        try {
            docker.image('qnib/pytest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
        }
        catch (e){
            echo 'Test Failed'
            throw e
        }
        finally {
            junit 'test-reports/results.xml'
        }
    }
    stage('Deliver'){
        try {
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                dir('dist') {
                    unstash 'compiled-results'
                    sh 'pyinstaller -F add2vals.py'
                }
            }
        }

        catch (e){
            echo 'Build Failed'
            throw e
        }

        finally {
            archiveArtifacts artifacts: 'dist/add2vals'
        }
    }
}