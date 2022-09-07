node {
    withEnv(
        ['FILE_LOCATION=/var/jenkins_home/simple-python-pyinstaller-app']) {
            stage('Build'){
                docker.image('python:2-alpine').inside {
                    sh 'python -m py_compile ${FILE_LOCATION}/sources/add2vals.py ${FILE_LOCATION}/sources/calc.py'
                }
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
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
        }
        catch (e){
            echo 'Build Failed'
            throw e
        }
        finally {
            def currentResult = currentBuild.result ?: 'SUCCESS'
                if (currentResult == 'UNSTABLE') {
                    archiveArtifacts 'dist/add2vals'
                    echo 'UNSTABLE BUILD'
                }

            def previousResult = currentBuild.previousBuild?.result
                if (previousResult != null && previousResult != currentResult) {
                    archiveArtifacts 'dist/add2vals'
                    echo 'Built Successfully'
                }
        }
    }
}