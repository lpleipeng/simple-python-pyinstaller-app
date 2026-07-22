pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh "python3 -m py_compile sources/add2vals.py sources/calc.py"
                stash includes: 'sources/*.py', name: 'pycode'
            }
        }
        stage('Test') {
            steps {
                sh """
                    python3 -m pip install pytest pytest-junit
                    mkdir -p test-reports
                    python3 -m pytest --junit-xml test-reports/results.xml sources/test_calc.py
                """
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'test-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                unstash 'pycode'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
    post {
        always {
            echo "构建执行完毕"
        }
    }
}
