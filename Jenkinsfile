node {
    stage('Build'){
        docker.image('python:2-alpine').inside {
            echo 'Test Poll SCM'
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
    stage('Deploy'){
            try {
                dir('dist') {
                    sh "docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-ragillio_aji/sources:/src cdrx/pyinstaller-linux:python2 'pyinstaller -F add2vals.py'"
                }
            }
            catch (e){
                echo e
                throw e
            }
            finally {
                archiveArtifacts 'sources/dist/add2vals'
                sh "docker run --rm -v  /var/jenkins_home/workspace/submission-cicd-pipeline-ragillio_aji/sources:/src cdrx/pyinstaller-linux:python2 'rm -rf build dist'"
            }
    }
}