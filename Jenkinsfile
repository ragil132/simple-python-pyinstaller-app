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
        withEnv(["VOLUME=$(pwd)/sources:/src, IMAGE=cdrx/pyinstaller-linux:python2"]) {
            try {
                dir('env.BUILD_ID') {
                    unstash 'compiled-results'
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
            }
            catch (e){
                echo e
            }
            // finally {
            //     archiveArtifacts '${env.BUILD_ID}/sources/dist/add2vals'
            //     sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
            // }
        }
    }
}