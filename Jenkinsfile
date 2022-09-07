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
                if (previousResult != null && previousResult != currentResult && currentResult == 'SUCCESS') {
                    archiveArtifacts 'dist/add2vals'
                    echo 'Built Successfully'
                }
        }
    }
}